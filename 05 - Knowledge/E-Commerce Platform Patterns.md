---
title: E-Commerce Platform Patterns
type: knowledge
status: active
owner: "Leo"
created: 2026-08-14
updated: 2026-08-14
ai_access: internal
ai_generated: true
review_status: draft
tags: [architecture, spring-boot, angular, patterns, philippines]
---

# E-Commerce Platform Patterns

Vertical-agnostic patterns extracted from `02 - Projects/Active/E-Commerce` (Home by Bami —
Angular 17 + Spring Boot 3). This is the transferable layer: what to carry into the next
product, and what not to repeat.

Source of truth for *how the coffee shop works* stays in that project's own
`memory.md` / `DECISIONS.md` / `docs/`. This file is only what survives a change of vertical.

**Baseline stack:** Angular 17 (standalone components + Signals, no NgModules, plain CSS with
custom-property tokens) · Spring Boot 3.2.5 / Java 17 · Spring Data JPA · JWT via jjwt 0.12.5 ·
Maven wrapper.

---

## Patterns worth reusing

### 1. Server-authoritative pricing

**The single best decision in the codebase.** The order request DTO contains **no price, no
subtotal, no total, no delivery fee** — only `productId` and `quantity`. There is nothing for a
client to tamper with.

- Line totals are computed from the `Product` entity read fresh from the DB, `HALF_UP` to scale 2.
- `unitPrice` is snapshotted from the DB at write time.
- The delivery fee is read off a server-persisted quote row, never from the request.

**Rule: never accept a money value from a client. Accept identifiers and quantities; compute
everything else.**

### 2. Single-use server-cached quote

Generalise this to *any* client-visible computed price — delivery fee, labour estimate, freight,
discount.

1. Server computes the price, persists a row keyed by an opaque `quotationId`, returns id + price.
2. Client displays the price and later submits only the `quotationId`.
3. On submit, `validateAndConsume(id)`: reject blank/unknown, reject already-`consumed`, reject
   expired, then flip `consumed = true` and read the authoritative values off the row.

Known gap in the original: the read-check-write on `consumed` has no pessimistic lock or
`@Version`, so the single-use invariant isn't actually enforced under concurrency. **Add
`@Version` or a `SELECT … FOR UPDATE` when reusing.**

Related decision worth copying: **the fee is locked at order time.** A fresher re-quote at
dispatch never changes what the customer already paid — the business absorbs the difference.
Re-quote using the *already-geocoded coordinates* so it can't resolve to a different place.

### 3. Denormalised order line snapshot

`OrderItem` stores `productId` as a plain `Long` — **no `@ManyToOne`** — alongside copied
`productName`, `unitPrice`, `quantity`, `lineTotal`. Renaming or repricing a product never
mutates historical orders.

Transfers unchanged to any product vertical.

### 4. External API client — six-part shape

The most transferable asset here. Both the courier and geocoding clients follow it.

| Part | How |
|---|---|
| **Credentials** | `@Value` **constructor** injection into `final` fields. Env-var indirection with empty defaults: `"${VENDOR_API_KEY:}"` |
| **Fail fast, degrade gracefully** | `isConfigured()` + `requireConfigured()` throwing `503` with an admin-actionable message. The app boots and runs fine with the integration absent; only that feature 503s. Ideal for a template repo. |
| **Two-layer DTOs** | Parse vendor JSON with Jackson's `JsonNode` **tree API**, not annotated POJOs. Lift only the fields you need into small internal records. Map to outward-facing DTOs via static `from()`. Vendor field churn touches one extraction line. |
| **One choke point** | A single `signedRequest(method, path, body)` is the sole egress. All auth and all error translation live in exactly one place. |
| **Three-tier error translation** | ① vendor error-id → customer-actionable copy · ② HTTP status → *admin-diagnostic* copy ("401 → check the API key", "402 → balance may be low") · ③ network/parse → generic retry. Log status + raw body server-side; **never return the vendor body to the client.** All vendor faults surface as `502`. |
| **Cache static reference data** | Double-checked locking on a `volatile JsonNode` for capability/city lookups. |

Also good: expose a **raw passthrough** endpoint for vendor enum-like data (e.g. service types)
so the frontend reads whichever values are actually valid for the market instead of the backend
hardcoding values that may not exist there.

**Add before reusing — all absent in the original:** HTTP timeouts (a hung vendor blocks a
thread indefinitely, and on the quote path that thread is inside a DB transaction), retry with
backoff, a circuit breaker, idempotency keys on order-creating POSTs, SLF4J instead of
`System.out.println`, and `@ConfigurationProperties` records instead of loose `@Value` strings
so config is validated at startup.

### 5. HMAC request signing and webhook verification

Canonical string, CRLF-delimited with a blank line before the body:

```
{timestamp}\r\n{METHOD}\r\n{path}\r\n\r\n{body}
```

HMAC-SHA256 over the shared secret, lowercase hex. Outbound token `apiKey:timestamp:hex`.
Compare with `MessageDigest.isEqual` (constant time). Fail closed on a blank secret or null
signature.

**Hard-won gotchas:**

- **The base URL must not include the version prefix** if the signed path already carries it —
  a doubled `/v3/v3/…` breaks verification with a confusing 401.
- Outbound and inbound timestamps may use **different units** (ms vs seconds). Read the spec.
- Sign the **`data` sub-node alone**, re-serialised — not the full envelope.
- **Always return HTTP 200**, even on a bad signature or unknown event. Vendors disable a webhook
  URL after enough non-200s, and a retry cannot fix a payload already rejected as invalid.
- Implement **`GET` on the webhook path** returning `"OK"` — registration probes the URL with a
  non-POST request and a 500 fails registration.

**Two real defects to avoid repeating:**
1. **No timestamp-freshness check** → any captured valid webhook is replayable forever. Reject
   timestamps outside ±5 minutes.
2. **Verifying over re-serialised JSON rather than the received bytes.** Jackson's output will not
   byte-match the sender's `JSON.stringify` in general (number normalisation, non-ASCII escaping,
   duplicate keys). It happens to match captured sandbox payloads. Any vendor formatting change
   silently invalidates *every* webhook — and because the endpoint always returns 200 and only
   logs to stderr, the failure is **completely silent**. HMAC over the exact substring of the raw
   payload, or accept the risk explicitly **and add alerting**.

### 6. Monotonic status progression guard

When two independent writers update the same status field (a push webhook and a manual poll) with
no chronological guarantee:

- Define an ordered `PROGRESSION` list giving each status a rank.
- Reject any incoming status ranked *below* the current one.
- Terminal exits (`CANCELLED`, `REJECTED`) always win from anywhere.
- Once terminal, drop **all** further updates, including metadata backfill.
- Funnel both writers through **one** apply method so they cannot diverge.

This was the best-engineered part of the original. Note the lesson that the same rigour was *not*
applied to the primary order status, which accepts any transition from any state including
`COMPLETED → RECEIVED`. **Guard both.**

### 7. Backend CRUD skeleton

Per-feature package, repeated near-identically:

```
entity → repository → response DTO → request DTO → service → public controller
       → admin controller → seeder
```

- DTOs are Java `record`s with a `static from(Entity)` factory. Mapping is manual — no MapStruct.
- Entity→DTO in the DTO; DTO→entity inline in the service via Lombok `@Builder`.
- Error idiom is `throw new ResponseStatusException(HttpStatus.X, "msg")` everywhere — no custom
  exception hierarchy. Cheap and fully domain-neutral.
- A private `findOrThrow(id)` per service.
- `ApiError` as a 5-field record + one `@RestControllerAdvice`.
- Seeders are `@Component implements CommandLineRunner` with an idempotence guard
  (`if (repo.count() > 0) return;`).

**Authorization by URL prefix, not annotations.** One rule — `/api/v1/admin/** → hasRole("ADMIN")`
— protects the entire admin surface. No `@PreAuthorize` anywhere. This is the single most valuable
convention to carry forward: admin surface protection stays one line.

**Public vs admin split:** public controllers are GET-only and return **active/available only**;
admin adds the write verbs and returns everything. *Apply this consistently* — the original forgot
it in exactly one package, so a sold-out flag was written but never read.

**Collapse the duplication before forking.** Four byte-identical single-field PATCH records, three
~90%-identical services, three near-identical admin controllers. A generic
`SortableToggleableService<T>` removes ~200 lines. Same on the frontend: the admin list-page
template and styles are copy-pasted three times, `toggleActive`-with-rollback three times, and
`uploadImage` byte-for-byte twice.

### 8. File upload

`WebMvcConfigurer` maps `/uploads/**` → `file:uploads/` (outside the jar). The controller rejects
empty files, applies a content-type allowlist, and **regenerates the filename as
`UUID.randomUUID() + ext`** so no client-supplied name reaches the filesystem. Size cap via
`spring.servlet.multipart.max-file-size`.

**Fix when reusing:** the allowlist keys off `file.getContentType()`, which is client-supplied and
trivially spoofed. Add a magic-byte check. Also **do not put user-private uploads (payment
receipts) under the same publicly-served root as product images.**

### 9. Frontend service shape

```ts
@Injectable({ providedIn: 'root' })
private api = inject(ApiService);
readonly items = signal<T[]>([]);
readonly loading = signal(false);
readonly derived = computed(() => …);
load() { this.api.get<T[]>('/path').pipe(catchError(() => of([]))).subscribe(d => this.items.set(d)); }
```

- **Signals only** — no NgRx, no `BehaviorSubject`. RxJS is the HTTP transport, nothing more.
- One ~31-line `ApiService` wrapper; **base URL lives in exactly one place**.
- `_items.asReadonly()` to keep writes internal.
- Functional `HttpInterceptorFn` and `CanActivateFn` with `inject()`.
- Two explicit error policies, chosen per service: *silent degrade* (`of([])`) for decorative data,
  *fail loud* for anything touching money. Document which and why — the original does.
- Admin shell separated from storefront chrome by checking the route prefix in the root component,
  so one bootstrap serves both apps.
- Cart persisted by an `effect()` writing to `localStorage`; rehydration is the signal's initial
  value, so it survives reload with no flicker.

### 10. Geocoding hardening (PH-specific, hard-won)

- **Normalise comma spacing** (`\s*,\s*` → `", "`) before every lookup — Google's parser is
  measurably spacing-sensitive; an unnormalised local address degraded to the country centroid.
- Append `", Philippines"`, and use **both** `components=country:PH` (hard filter) and `region=ph`
  (ranking bias) — stronger together.
- **Drop results typed `country`** — otherwise a garbage query passes the country centroid through
  as a real-looking coordinate, and the courier then rejects it with a confusing
  "out of service area".
- Surface non-`OK` provider statuses as `502` with the provider's own `status` + `error_message`
  — `REQUEST_DENIED` / `OVER_QUERY_LIMIT` mean a key or billing problem, not a bad address.
- **Pasted Maps URL:** prefer the embedded place pin (`!3d…!4d…`) over the panned viewport
  (`@lat,lng,`) — cheaper *and* more precise than a text search.
- **A place URL's own name beats reverse-geocoding its coordinates** for labelling — two nearby
  POIs can reverse-geocode to the wrong business.
- Keep the API key **server-side only**; a text lookup at checkout doesn't need a browser SDK.

**Ambiguity detection worth copying:** keep the top-ranked result plus any candidate more than
~1.5 km away (inline haversine), and if more than one survives, return the candidate list and let
the user choose — *before* spending a vendor API call. Geocoding jitter for one real place is well
under 1 km; a name existing in two barangays is many km apart.

### 11. Interactive map without a billed key

Leaflet + raw OpenStreetMap tiles. No API key, no billing, no scrapeable key in the browser.
Deliberately chosen over the Google Maps JS SDK because Places Autocomplete has **no free tier at
all**, while Geocoding has 10,000/month. A draggable marker with an emoji `divIcon` needs a global
style — `divIcon` escapes component style scoping.

Re-quote only on an explicit "confirm new location" press, not on every drag, so one gesture isn't
one vendor call per pixel.

### 12. Hibernate coordinate precision trap

**Every lat/lng column needs an explicit `@Column(precision = 11, scale = 8)`.** Hibernate
silently defaults an unannotated `BigDecimal` to scale 2, rounding persisted coordinates to ~1 km.
Same-request JSON responses never show it — only a later read of a persisted row does.

### 13. Location capture is three separate concerns

Store separately, never cross-derive: **a geocoded coordinate** (fee and routing maths), **free-text
rider notes** (unit, gate, landmark — for a human), and **prep notes** (a different audience
entirely). Normalise phone numbers to E.164 for the wire only; leave the stored value untouched.

---

## Anti-patterns — do not repeat

| Anti-pattern | Why it hurt |
|---|---|
| **No config/branding layer** | Business name, address, currency symbol, tax rate and palette scattered as hardcoded defaults across services, ~20 templates, and CSS token *names*. This is the single largest obstacle to reusing the codebase. **Build one injectable config provider first.** |
| **Mock fallback wired into a shipping code path** | A catalog service that serves hardcoded demo products on any HTTP failure — and in one method *unconditionally*. A backend hiccup shows customers the wrong shop's inventory. Dev convenience, production liability. Delete before forking. |
| **Hardcoded tax rate, duplicated on both sides** | `0.0875` as a `private static final` in the service *and* a magic number in the cart. Wrong for the market and guaranteed to drift. Tax is configuration, and the server total is the only one that counts. |
| **String-concatenation codec with no escaping** | `k1:v1;k2:v2` in one VARCHAR. Fine for `Oat`/`Regular`; corrupts irrecoverably the moment a value contains `;` or a key contains `:` — which parts data does immediately (`Fitment: CB150R; CB150X`). Use a JSON `@Converter`; you also get nested and typed values. |
| **A quality checklist with no tests behind it** | A genuinely good 6-phase CX standard, described as "acceptance criteria and regression guardrails", violated in at least two places — because there was **one spec file across ~180 source files**. A checklist without tests is a document, not a guardrail. |
| **Template-driven forms with hand-rolled validation** | `ngModel` with no `<form>` element: no `ngSubmit`, no Enter-to-submit, no dirty/touched state. Regexes and per-field error signals re-implemented in every component; modals that silently `return` on invalid input so the button appears dead. Build one shared field component. |
| **`effect()` writing to a signal without `allowSignalWrites`** | Throws NG0600 on Angular 17 — and only on the code path that needs a real order to reach a particular state, so it survives manual testing. |
| **Side-effecting service constructors** | HTTP fired from the constructor means DI order determines request order and nothing can defer or cancel. Uncleared `setInterval` in a root service polls forever, including on routes that don't use it. |
| **`System.out.println` for integration logging** | No levels, no structure, no aggregation — and in the original it logged every webhook header and full body, unredacted, including driver PII. |
| **`ddl-auto: update` with no migration tool** | Acceptable for a prototype. Not for long-lived relational data, and actively dangerous once two products share a lineage. Add Flyway before forking. |
| **Random short business keys on a unique column** | `"ORD-" + random 6 digits` with no collision check: ~50% chance of a collision by ~1,100 orders, surfacing as a 500 on an **already-paid** checkout after the quote was consumed. Sequence, or check-and-retry. |
| **Guessable public identifiers on an unauthenticated endpoint** | A short random order number doubling as the public tracking id, on a `permitAll` route with no rate limit, returning full customer PII. Use an unguessable token for public lookup, decoupled from the human-readable reference. |
| **OCR treated as verification** | Extracting a reference number from a receipt screenshot is *data entry*, not verification. Without amount, payee and timestamp checks — plus a uniqueness constraint on the reference — one genuine receipt validates unlimited orders. |
| **Native/blocking work inside `@Transactional`** | OCR, file I/O and an un-timeout'd HTTP call in the same transaction as the order write. Connection-pool exhaustion under trivial load. |
| **Non-thread-safe native wrapper as a singleton field** | Tesseract's `TessBaseAPI` handle shared across concurrent requests: garbage output or a JVM-level crash, with the evidence swallowed by a bare `catch (Exception)`. `ThreadLocal` or a pool. |
| **Bare `catch (Exception) { return null; }`** | A missing native library, missing training data, an unreadable image and a genuine no-match all become the same silent `null`. Fully degraded OCR is indistinguishable from bad receipts. |
| **Echoing exception messages to clients** | `include-message: always` plus a catch-all handler returning `ex.getMessage()` leaks SQL and driver detail to the browser. |
| **Singleton settings row with no bootstrap guarantee** | A settings entity with a hand-assigned id and a service that 500s if the row is missing — while `CommandLineRunner` seeders run *after* the web server starts accepting traffic. |
| **Un-abstracted `now()`** | Direct `LocalDate.now()`/`LocalTime.now()` in open/closed logic: untestable without freezing system time, and wrong when the server zone differs from the business's. Inject a `Clock`. |
| **Eager `@ManyToOne` + unbounded `findAll()`** | N+1 on the main catalog and admin board endpoints. Nothing paginates anywhere. |
| **Secrets committed without env indirection** | Vendor keys correctly used `${VAR:}`; the JWT signing secret sat as a plaintext literal two lines above them. Anyone with repo access forges admin tokens. **Audit every secret, not most of them.** |
| **Seeded admin credentials as compile-time constants** | `public static final` email and password, with no `@Profile` guard and no way to override per environment — and republished in the README. |
| **A11y as an afterthought** | Modals with no `role="dialog"`, no focus trap, no Escape; toasts with no `aria-live` (so every error is silent to a screen reader); cards as clickable `<article>` elements with nested buttons. Cheap at this stage, expensive later. |

---

## Related

- Project detail: `02 - Projects/Active/E-Commerce/` — `memory.md`, `DECISIONS.md`, `docs/`
- Next vertical: [[05 - Knowledge/Motorcycle Vertical - Build Plan.md]]
- Commercial terms: [[01 - Business/Operations/Client Contract Template.md]]

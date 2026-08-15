---
title: Motorcycle Vertical — Build Plan
type: knowledge
status: parked
owner: "Leo"
created: 2026-08-14
updated: 2026-08-14
ai_access: internal
ai_generated: true
review_status: draft
tags: [architecture, planning, motorcycle, reuse]
---

# Motorcycle Auto Supply — Build Plan

What transfers from the coffee shop build, what has to be written, and what to fix in the coffee
codebase *first* so the fork is cheap.

> [!warning] This vertical is parked
> Per [[GOALS.md]]: coffee ships first. This document exists because the code was fresh in mind
> on 2026-08-14, not because building it is next. The forcing date is **2026-09-30** — POS done
> and the money conversation held. Building motorcycle features before then is avoidance.

**Target business:** motorcycle auto supply — parts sales plus workshop services (PMS etc.) with
scheduled appointments.

**Patterns reference:** [[05 - Knowledge/E-Commerce Platform Patterns.md]]

---

## Verdict in one line

The **commercial spine transfers** — auth, pricing integrity, payment rail, courier stack, admin
console shape, design tokens. The **catalog model and the scheduling model do not exist** and are
the real build.

---

## Transfer matrix

### Reusable as-is

| Component | Note |
|---|---|
`auth/` + `config/` (backend) | JWT issue/validate, filter, role enum, security chain, `ApiError` + handler, upload config. ~470 lines of genuine platform code. Zero domain knowledge. |
`faq/` package | All 9 files. Content-only changes. |
`GeocodingService` | Vertical-agnostic, PH-tuned, the best-hardened code in the project. |
HMAC signer + courier client + quote/dispatch services | Change `default-service-type`; see caveat below. |
External-API client pattern | Add timeouts, retries, SLF4J, idempotency keys. |
`Order` → `OrderItem` snapshot model | Handles parts sales nearly unchanged. |
Server-authoritative pricing | Copy verbatim. Non-negotiable. |
Single-use server-cached quote | Generalise to labour estimates and freight. |
Frontend: `api.service`, `auth.service`, `notification.service`, interceptor, all three guards, `image-url.util` | Rename two localStorage keys; add 401→logout and an interceptor origin allowlist. |
Shared components: `modal`, `toast`, `badge`, `faq-accordion`, `loading-skeleton` | Fix the a11y gaps once, benefit twice. |
Admin shell pattern + admin service pattern | List-signal + optimistic toggle + toast-on-error. Copy-paste. |

### Reusable with config

- **`promotion/`** — new seed content. Consider adding `startsAt`/`endsAt`; the entity has no date
  range today, so "This Week Only" expires by manual toggle.
- **`store/`** hours layer — `StoreSettings` / `DaySchedule` / `StoreClosure` / emergency pause
  transfer directly as the shop's operating-hours gate. Add a `storeName` field (it's a
  `static final` constant today) and rename the order-oriented buffer fields.
- **CSS token layer** — recolouring is ~15 custom properties plus a fonts link. But **rename the
  tokens to semantic names first** (`--color-espresso` → `--color-accent`); the existing names
  already lie about their values.
- **Courier service type** — `MOTORCYCLE` is the default. Ironic here: a motorcycle courier
  cannot carry a tyre or a battery. Needs per-order service-type selection by weight and
  dimensions. The raw `/service-types` passthrough already exposes the valid values.

### Needs rework — keep the skeleton, replace the model

| Item | Change |
|---|---|
`Product` | Add `sku`, `brandId`, `oemNumber`, **`stockQuantity`**. Today `available` is a boolean; parts retail is stock-driven. `rating` is dead weight — no review entity exists behind it. |
`Category` | Add `parent` self-reference + `sortOrder`. It's flat today; parts need Engine → Piston Kits → Rings. |
`Customization` + `toCustomizations()` | **Delete.** See below. |
`admin-product-form-modal` | Three literal drink checkboxes hardcoded in the UI, matched by a `switch` on `"MILK"/"SUGAR"/"TEMP"` in the service. Admins cannot author an arbitrary option. **Biggest single rework item.** |
`OrderOptionCodec` | Rewrite as a JSON `@Converter`. No escaping today; parts values contain `;` and `:` immediately. |
`order-status-stepper` | Make `steps` an `@Input`. Labels are pickup-food today, and delivery orders are already shown "Ready for Pickup" — an existing bug. |
`checkout-page` | 437-line god component. `pickupTime` is required even for delivery; must become optional and date-aware. Split it. |
`cart.service` tax | Extract the rate to config, or go VAT-inclusive. |
`account-page` | A stub. Becomes the "my garage" + service history surface. |
GCash OCR rail | Keep as a *fallback* only. Add amount + payee extraction and a uniqueness constraint on the reference, or move to a real PSP (PayMongo / Xendit) as the primary rail. |

### Must build from scratch

Three capabilities, none of them small.

---

## 1. Parts fitment

**`Product` + `Category` + `Customization` cannot model this.** Reasons, concretely:

- `Customization` is `@Embeddable`, stored per-product in an element collection — each product owns
  a private *copy*. There is no shared `VehicleModel` row to join against, so *"every part that
  fits a 2019 Honda Click 125i"* is a many-to-many query with no join table to support it.
- Options are a **CSV string**. Matching a vehicle means `LIKE '%Click 125i%'` — unindexable, and
  false-positives across similar model names.
- Only three hardcoded option sets can ever exist.
- **Wrong axis semantically.** `Customization` models buyer-selected variation at add-to-cart time
  and flows into the order payload. Fitment is a *catalog constraint*. Overloading one for the
  other corrupts orders.

**Build:**

```
VehicleMake ──1:N──> VehicleModel ──1:N──> PartFitment <──N:1── Product
                     (yearFrom, yearTo,     (yearFrom?, yearTo?,
                      displacementCc)        position?, notes)
```

- `PartFitment` is a **real entity**, not a plain `@ManyToMany`, so a pairing can carry
  year-narrowing, fitment position (front/rear), and notes.
- `ProductRepository` gains `findByFitments_VehicleModel_Id(Long)`.
- The existing `getProducts(categoryId, search)` if/else ladder becomes a JPA `Specification` once
  a third filter lands.
- Frontend needs: make/model/year selector ("my garage", persisted), part-number / OEM
  cross-reference lookup, hierarchical category navigation, and multi-facet filtering (brand,
  price, position). **None of this exists** — today it's one flat category id plus free-text search
  matched against `name` only.

## 2. Service appointments

**Do not fork `Order`. Do not bend `DaySchedule` into slots.**

What the hours layer genuinely gives you: a weekly open/close grid, dated holiday overrides, an
emergency kill switch, and an "is the business open right now" evaluation chain. That's an
*operating-hours* layer and it transfers verbatim.

What it cannot do: it answers only *"is the shop open now"*, never *"is a bay free at 2pm
Thursday"*. There is no slot concept, no capacity model, one window per day (no lunch break), no
booking entity, no timezone handling, and every method is hardcoded to *today*.

**Build a separate `Appointment` aggregate beside the order, not inside it:**

```
Appointment
  ├── slot interval (typed start Instant + duration — not a free-text String)
  ├── ServiceType      (duration, standard price)
  ├── ServiceBay / Technician   (the capacity constraint)
  ├── Vehicle          (make/model/year/plate/VIN/odometer)
  ├── AppointmentStatus
  └── Order?           (nullable FK — the money side)
```

- Cash-and-carry parts sale → `Order`, no `Appointment`.
- Service booking → both.
- Service job consuming parts → `OrderItem` rows on the linked order.
- `FulfillmentType` gains a third mode (`IN_STORE_SERVICE`) — nothing ships; the customer *brings*
  the subject in.

**Also missing and required for a real job card:**

- **Labour lines.** `OrderItem` is `unitPrice × int quantity`. Labour is `rate × fractional hours`,
  with an assigned technician and estimated-vs-actual. Needs a discriminator or a sibling
  `ServiceLineItem`.
- **Double-booking prevention.** Nothing objects today because there is no slot entity.
- **Estimate revision.** Workshops discover extra work mid-job and re-quote. Order totals are
  computed once at placement with no amendment path and no revision history.
- **Customer FK.** Orders are guest-only. Service history per vehicle needs an owner.
- **Slot generation service** intersecting the `DaySchedule` window with existing bookings and bay
  capacity.

## 3. Inventory

Does not exist in any form. `Product.available` is a boolean. Parts retail needs stock quantities,
reorder thresholds, low-stock alerts, and deduction on order. Note the existing bug worth not
inheriting: **availability is never checked at order time at all** — the flag is written and never
read, on either side.

---

## Do these in the coffee codebase first

Cheaper once, in one product, than twice after divergence. None of these are motorcycle features —
they're all improvements to what ships to Bami.

1. **Rename the package root** to something neutral (`com.<yourco>.commerce`) and the Maven
   coordinates with it. Mechanical now, painful after divergence.
2. **Build one injectable config/branding provider** — business name, address, currency, tax
   policy, fulfillment modes, palette. Today all of it is hardcoded across services, ~20
   templates, and CSS token names. **This is the single highest-leverage refactor.**
3. **Delete `mock-data.ts`** and its fallback paths.
4. **Rename CSS tokens to semantic names.** Then reskinning is editing `:root` only.
5. **Extract the tax rate to config** and make the server total authoritative.
6. **Collapse the duplication** — the generic toggleable-sortable service, the shared admin
   list-page component, one `uploadImage`, one shared form-field component. ~400 lines across both
   halves.
7. **Add Flyway.** `ddl-auto: update` cannot survive two products sharing a lineage.
8. **Add timeouts and retries** to both API clients, and SLF4J throughout.
9. **Write tests for the CX checklist.** One spec file across ~180 source files is why a good
   standard was violated in at least two places. Cart maths, guards, the availability filter, and
   the payment flow are the ones that matter.
10. **Fix the a11y basics in `shared/`** — dialog role, focus trap, Escape, `aria-live`. Two
    products inherit the fix.

## Sequencing

| Phase | Work |
|---|---|
Now → 2026-09-30 | Coffee only. POS MVP (~41h), then the money conversation. |
After client 1 signs | Pre-deployment fixes, then the 10 refactors above (~30h). |
Then | Templatize — coffee becomes a configurable template. Clients 2–5 at ~40h each. |
Only then | Motorcycle. Fitment + appointments + inventory is a genuine second product, not a reskin. |

**Estimate honestly when the time comes:** the transferable spine saves real weeks, but fitment,
appointments and inventory are ~150h+ of new domain work on their own. Motorcycle is not "coffee
with different pictures."

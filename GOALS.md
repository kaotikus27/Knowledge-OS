---
title: Goals
type: goals
status: active
owner: "Leo"
created: 2026-08-14
updated: 2026-08-14
ai_access: internal
ai_generated: true
review_status: draft
---

# Goals

## Current Objective

**Land, deliver, and get paid by one coffee shop client — then turn that build into a
reusable template.**

Revised 2026-08-14 from "acquire 5 paying clients." The 5-client goal survives as the
medium-term target (~Q2–Q3 2027) but is not the thing to work backward from at 10h/week.
One delivered client produces the case study, the testimonial, the referral engine, and the
template. Nothing else unlocks client 2.

**Vertical:** coffee shops with pastry. Motorcycle auto supply waits until coffee ships.

## Current State

Baseline captured 2026-08-14.

**Capacity:** 10 focused hours/week (2 hours/day, weekdays).

**Time allocation:**

| Activity | Share | Hours/week |
|---|---|---|
| Technical setup | 60% | 6.0 |
| Task management / planning | 30% | 3.0 |
| Actual outreach | 10% | 1.0 |

**Product — far more complete than assumed.** `02 - Projects/Active/E-Commerce`
(Angular + Spring Boot, 105 backend files, ~76 frontend components):

- Done: auth/JWT/roles, catalog + admin CRUD + image upload, cart → checkout → order flow,
  GCash payment verification (OCR), Lalamove delivery dispatch + webhooks + geocoding, store
  hours/closures/emergency pause, promotions, FAQs, full admin console (orders board,
  history, products, settings)
- **Missing: POS.** No register, till, shift open/close, or cash drawer anywhere in the
  codebase. The offer promises "point of sale"; the admin orders board is order management,
  not POS.
- Also missing: inventory/stock tracking, sales reporting, test coverage (one spec file)

**Commercials:** price range now set (below). No contract, no signature, no payment received.
Never been paid for this work before — no price anchor from history.

**Pipeline:** one coffee shop client, actively engaged. Being shown the prototype and kept
across the work as it progresses. **No money has changed hands and nothing is in writing.**

**Unsecured exposure:** ~250h already invested (105 backend files, ~76 components, GCash OCR,
Lalamove dispatch), plus ~140h remaining. At ₱400/h that is ~₱100,000 of value already
delivered on a handshake, tracking toward ~₱156,000 before price is ever discussed.

## Plan

**The binding constraint is not outreach hours — it is that a sellable product has never
been shown to a buyer.** The prototype intended for a client dry run already exists.

1. **This week (wk 33–34):** stop building. Demo the existing system to the prospective
   coffee shop client. Get the contract signed and the discovery fee paid before any further
   hours go in.
2. **Let the client scope v1.** Ask whether POS is needed at launch or whether online
   ordering alone is enough. If POS defers to v2, ~60h and ~2 months come off the timeline.
3. **Reallocate to 8h build / 2h sales** (or 6h/4h on the aggressive path).
4. **Client 1 live:** ~Oct 2026 if POS defers to v2; ~mid-Dec 2026 if POS is in v1.
5. **Then templatize** (~30h): config-driven branding and catalog, remove hardcoded
   `com.bakery` and seeders, so clients 2–5 cost ~40h each instead of ~190h.
6. **Clients 2–5:** referral-led, not cold. Coffee shops cluster geographically and owners
   know each other — a well-delivered client 1 is the acquisition channel. Target ~Q2–Q3 2027.

**Pricing — decided 2026-08-14.** Low entry fee, recover on recurring:

| Item | Amount |
|---|---|
| Build fee | **₱50,000–80,000** (50% to start production phase, 50% at launch) |
| Monthly — hosting, maintenance, support | **₱5,000–8,000/mo**, billed in advance |

3-year value at the midpoint ≈ **₱299,000**, versus ≈ ₱261,000 on a ₱120k–150k setup with a
low monthly. Recurring at 5 clients ≈ **₱32,500/mo** rather than ₱17,500/mo — this is the
choice that makes the model viable.

**Two conditions or the model collapses:**
1. **Leo hosts it.** The moment the client self-hosts, the monthly goes to zero.
2. **The monthly must be justified** as hosting + GCash/Lalamove upkeep + support + updates —
   not framed as rent.

Contract: [[01 - Business/Operations/Client Contract Template.md]].

**Timing — decided 2026-08-14, against advice.** The money conversation happens *after* POS is
built, on the reasoning that a complete offer negotiates better. Recorded objection: it spends
~60 more unpaid hours to enter the same conversation from a weaker position, and maximum
leverage (engaged client, working demo, work not yet done) is expiring now.

**Mitigation — do both:**
- **Defuse the free-work assumption at the next demo.** Not a negotiation, one sentence:
  *"Once we're at deployment there'll be a build fee and a monthly for hosting and support —
  I'll put the numbers together for you."* Converts their assumption from *free* to *pending*
  at zero cost.
- **Hard date: 2026-09-30.** POS done and money conversation held. **If POS is not finished by
  then, the money conversation happens anyway.** The date is the commitment, not the feature.

**Minimum viable POS — ~41h. Everything else is v2.**

| Component | Hours |
|---|---|
| Register UI — products, quantity, modifiers, running total | 12 |
| Payment capture — cash/GCash/card, tendered, change | 8 |
| Receipt — order number, on-screen + print | 6 |
| Shift open/close — starting cash, count, X/Z report | 10 |
| Void / refund with reason | 5 |

`Order`, `OrderItem`, `PaymentMethod`, and `PaymentStatus` already exist. POS is largely a new
UI over existing infrastructure — reuse, do not rebuild the order model.

**Deferred to v2, deliberately:** offline mode, multi-register sync, cash drawer hardware,
barcode scanning, inventory deduction, split payments, loyalty.

## Risks & Time-Sensitive Factors

- **Avoidance risk (primary).** A demo-ready product plus 1h/week of selling is the whole
  problem in one sentence. Watch for the technical-setup bucket regrowing after any
  reallocation, and for POS becoming the new reason not to sell.
- **Unpaid work risk — now the top risk.** ~250h delivered, ~140h to go, nothing in writing,
  no downpayment, price deliberately deferred until after POS. Every hour before signature is
  unsecured, and each free hour delivered lowers the price the client will accept, because it
  teaches them the work is free.
- **No forcing function.** No deadline, no price agreed, no contract — the three absences that
  perfectionism needs to survive. Nothing in the current setup can force this to ship. Hence
  the invented 2026-09-30 date; an arbitrary deadline told to someone else beats a perfect
  plan told to nobody.
- **POS becoming the new reason not to sell.** An undated milestone now gates the money
  conversation. Watch for POS scope expanding past the 41h MVP.
- **IP risk.** The templatize strategy dies if client 1's contract assigns exclusive rights
  to the codebase. The contract must retain reusable components and framework for the
  developer.
- **Business-model mismatch.** 5 clients × ~₱3,500/mo = ~₱17,500/mo recurring. This is an
  agency (income from project fees), not SaaS. Agency scales on price per project, not client
  count — which argues for fewer clients at higher prices, not more.
- **GCash OCR fraud exposure.** Verifying payment from screenshot OCR is forgeable. Needs a
  manual-review fallback before real money flows.
- **No runway data captured.** No savings clock or external deadline known. If one exists it
  changes the urgency of everything above.
- **Two verticals, two products.** Coffee (modifiers, prep queue) and motorcycle (parts
  compatibility, vehicle history, service scheduling) share little. Sequencing them is
  correct; running them in parallel would halve effective capacity.

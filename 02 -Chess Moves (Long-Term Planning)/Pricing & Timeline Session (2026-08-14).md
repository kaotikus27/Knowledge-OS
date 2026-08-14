---
title: Pricing & Timeline Session
type: chess-moves
status: active
owner: "Leo"
created: 2026-08-14
updated: 2026-08-14
ai_access: internal
ai_generated: true
review_status: draft
---

# Leo — Chess Moves (August 14th 2026)

Session run straight out of `brain-setup`. Purpose was to set a price point and a deadline so
"highest-leverage activity" becomes an answerable question instead of a vibe.

It turned into something else halfway through, because the codebase contradicted the story.

## What I said I wanted

Claude as a strategic thinking partner who pokes holes rather than agrees. Prime directive:
figure out the most useful use of my time. Highest-leverage activities. Kill noise.

## The starting numbers

- 10 focused hours/week (2h/day, weekdays)
- Sole bottleneck named: first 5 paying clients
- Allocation: 60% technical setup (6h), 30% planning (3h), 10% outreach (1h)

## Hole #1 — the allocation

Nine of ten hours go to activities that cannot produce a client, against a self-declared
bottleneck of client acquisition. 1h/week of outreach is a rounding error, not a strategy.

Setup and planning are comfortable, legible, and carry zero rejection risk. Outreach carries
rejection. The allocation isn't a scheduling accident.

## Hole #2 — "5 clients" was never a metric

No date. No price point. No pipeline count. 5 clients by December at ₱50k and 5 by October at
₱500k are unrelated plans. Couldn't be worked backward from.

## Hole #3 — the arithmetic nobody had run

Scoped the offer honestly at ~190h/client for a solo dev at MVP quality. Against 10h/week:

- 1 client custom = ~19 weeks (4.5 months)
- 5 clients custom = ~95 weeks (**~2 years**)

So the original goal was a 2028 goal. And if outreach had worked perfectly and five clients
signed at once, I could not have delivered — five angry clients in a small local market where
referrals are the entire acquisition channel.

Delivery capacity was the binding constraint, not outreach. That reframe survived the session.

## Hole #4 — two verticals means two products

Coffee shops need modifiers, prep queues, fast repeat-order flow. Motorcycle auto supply needs
parts-to-model compatibility, vehicle service history, appointment/PMS scheduling. Different
data models. Serving both custom = two systems with one client each, the worst possible ratio.

**Decision: coffee shop + pastry first. Motorcycle waits until coffee ships.**

## Hole #5 — the free prototype

Plan was to hand over a functional frontend for a client dry run, unpaid, then follow with
backend. That's 40–60h of unsecured work handed to someone with zero commitment, inviting
endless revision against a documented perfectionism weakness.

Prototypes are fine. Unpaid prototypes are not. Charge a non-refundable discovery fee — it
filters tire-kickers and pays me to think.

## The correction — I was wrong about where I was

Then we looked at `02 - Projects/Active/E-Commerce`. 105 backend Java files, ~76 frontend
components. Angular + Spring Boot.

**Already built:** auth/JWT/roles, catalog + admin CRUD + image upload, cart → checkout →
order flow, GCash payment verification via OCR, Lalamove delivery dispatch with HMAC-signed
webhooks and geocoding, store hours/closures/emergency pause, promotions, FAQs, full admin
console with live orders board.

GCash and Lalamove integration are the hard, Philippines-specific parts. They're done.

**So the 190h estimate was wrong.** Remaining is ~140h, and more importantly:

> **The prototype I was "planning to build for a client dry run" already exists. I could demo
> it on Monday.**

That converts hole #1 from a suspicion into a receipt. I have a demo-ready product and I am
still building instead of showing it to anyone. Not a scoping problem. Not a pricing problem.

## The actual gap

**There is no POS in the codebase.** No register, till, shift open/close, cash drawer. The
offer promises point-of-sale; the admin orders board is order *management*. Also missing:
inventory/stock tracking, sales reporting, and test coverage (one spec file total).

Remaining scope: POS 40–60h, inventory 15–20h, reports 10–15h, hardening/tests 20–25h,
production deploy 15–20h, docs + client training 10h, client revisions 15h. ≈ 140h.

## Timeline options

| Path | Split | Client 1 live |
|---|---|---|
| Build-only (current behavior) | 10h build / 0h sales | ~late Nov 2026, **no client to deliver to** |
| Recommended | 8h build / 2h sales | ~mid-Dec 2026, paid, under contract |
| **Aggressive — demo now, POS to v2** | 6h build / 4h sales | **v1 live ~Oct 2026** |

The aggressive path is only available *because* of what's already built. Small coffee shops
may not need POS on day one — they need online ordering. **Let the client decide whether POS
is in v1.** If it defers, ~60h and ~2 months come off.

## Pricing

Directional. Validate against 2–3 local quotes before committing — no reliable PH market data
in hand and false precision would hurt me here.

| Phase | Deliverable | Payment |
|---|---|---|
| Discovery + prototype | Requirements, dry run of what exists | ₱15k–25k upfront, non-refundable |
| Build | POS/remaining, integrations, deploy | 50% down, 50% on acceptance |
| Live | Hosting, maintenance, support, minor updates | ₱2,500–5,000/mo in advance |

Setup total ≈ ₱80k–150k.

## The business-model realization

5 clients × ₱3,500/mo = **₱17,500/mo recurring.** That is not a living. The setup fees are
the income.

So this is an **agency**, priced like a product. Nothing wrong with agency — but agency scales
on price per project, not client count. Which argues for *fewer clients at higher prices*, the
exact opposite of the goal I walked in with. Worth sitting with before chasing 5.

## Gameplan

1. **Stop building this week.** Demo what exists to the prospective coffee shop client.
2. **Contract signed + discovery fee paid** before another hour goes in. See
   [[01 - Business/Operations/Client Contract Template.md]].
3. **Ask the client to scope v1** — POS at launch, or ordering only?
4. **Reallocate** to 8h/2h, or 6h/4h on the aggressive path.
5. **Ship client 1.** Oct–Dec 2026 depending on v1 scope.
6. **Templatize** (~30h): config-driven branding/catalog, kill hardcoded `com.bakery` and
   seeders. Drops clients 2–5 to ~40h each.
7. **Clients 2–5 referral-led.** Coffee shops cluster and owners talk. A well-delivered client
   1 *is* the channel. ~Q2–Q3 2027.

## Open threads

- **Price validation** — get 2–3 local quotes for comparable work
- **Runway** — never captured. Savings clock? External deadline? Changes all urgency
- **GCash OCR is forgeable.** Screenshot OCR as payment proof needs a manual-review fallback
  before real money moves through it
- **My own hard lines** — `me.md`'s Rules & Boundaries is all rules for Claude, none for me
- **Client 1 status** — have they committed to anything? Has any money changed hands?
- **Motorcycle vertical** — parked. Revisit after coffee ships

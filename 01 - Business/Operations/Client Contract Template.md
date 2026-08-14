---
title: Client Contract Template
type: template
status: active
owner: "Leo"
created: 2026-08-14
updated: 2026-08-14
ai_access: internal
ai_generated: true
review_status: draft
tags: [contract, business, operations]
---

# Client Contract Template — Web System Development

> [!danger] Not legal advice — get this reviewed
> Drafted by AI, not a lawyer, and not validated against Philippine law. Have a PH lawyer
> review before you sign anything. Pay particular attention to Clause 9 (IP), Clause 14
> (liability), and the BIR/tax provisions — these are the ones that hurt if wrong.

> [!warning] Clause 9 is load-bearing for your business model
> Section 9.2 retains your rights to reusable components and framework code. **Do not let a
> client strike it.** Without it you cannot reuse this codebase for clients 2–5, and the
> entire templatize strategy dies with the first signature.

**Fill before sending:** `[CLIENT NAME]`, `[BUSINESS ADDRESS]`, `[DATE]`, all `₱[  ]` amounts,
`[N]` day/round counts, and the Scope tables in Schedule A.

---

## SOFTWARE DEVELOPMENT AND SERVICES AGREEMENT

This Agreement is entered into on **[DATE]** by and between:

**Developer:** Eleomar Halaman ("the Developer"), of [YOUR ADDRESS], and

**Client:** **[CLIENT NAME]**, a business operating as [BUSINESS TYPE] at [BUSINESS ADDRESS]
("the Client").

---

### 1. Scope of Work

1.1 The Developer will design, develop, and deploy a web-based system as specified in
**Schedule A — Scope of Work**.

1.2 Work proceeds in the phases set out in Schedule A. Each phase must be accepted under
Clause 6 before the next begins.

1.3 **Anything not written in Schedule A is out of scope.** Verbal requests, chat messages,
and "can you just add…" do not modify scope. Only a signed Change Order under Clause 5 does.

1.4 Explicit exclusions unless listed in Schedule A: hardware, receipt printers, barcode
scanners, cash drawers, third-party subscription fees, content writing, product photography,
data entry of the Client's catalog, staff training beyond the sessions in Schedule A, and
integration with any system not named in Schedule A.

---

### 2. Fees

| Phase | Deliverable | Amount | Due |
|---|---|---|---|
| 1 — Discovery | Requirements document, system walkthrough / dry run | **₱[  ]** | On signing. **Non-refundable.** |
| 2 — Build (start) | Commencement of development | **₱[  ]** (50% of build fee) | Before development begins |
| 2 — Build (completion) | Acceptance per Clause 6 | **₱[  ]** (50% of build fee) | Within [7] days of acceptance |
| 3 — Live | Hosting, maintenance, support | **₱[  ] / month** | Monthly, in advance |

2.1 **Total setup fee: ₱[  ]**, exclusive of third-party costs under Clause 3.

2.2 All amounts are in Philippine Pesos.

2.3 The Discovery fee is non-refundable once paid, including where the Client elects not to
proceed to Phase 2.

2.4 **No work begins on any phase until that phase's payment has cleared.**

2.5 Late payment: amounts unpaid [15] days after the due date accrue interest at [2]% per
month, and the Developer may suspend all work and services under Clause 11.

2.6 Taxes: fees are [inclusive / exclusive] of applicable taxes. The Developer will issue
official receipts in accordance with BIR requirements. Any withholding tax required of the
Client shall be supported by the corresponding BIR Form 2307.

---

### 3. Third-Party Costs

3.1 The following are paid by the Client, directly or as pass-through at cost with no markup:
domain registration and renewal, hosting and server fees, SSL certificates, payment gateway
fees, delivery platform fees (e.g. Lalamove), SMS or email service fees, map/geocoding API
fees, and any paid third-party library or service required by Schedule A.

3.2 The Developer will provide an estimate of recurring third-party costs before Phase 2
begins. Estimates are not guarantees; third-party providers set their own prices.

3.3 The Developer is not liable for outages, price changes, policy changes, or discontinuation
of any third-party service.

---

### 4. Revisions

4.1 Each phase includes **[2] rounds of revisions** against the deliverables in Schedule A.

4.2 A "round" means one consolidated set of written feedback. Feedback delivered piecemeal
over multiple messages may be treated as separate rounds.

4.3 Revisions beyond the included rounds are billed at **₱[  ] per hour**, estimated and
approved in writing before work proceeds.

4.4 Revisions are corrections and refinements within the agreed scope. Requests that add,
remove, or change functionality are Change Orders under Clause 5.

---

### 5. Change Orders

5.1 Either party may propose a change to scope. The Developer will respond with a written
Change Order stating the work, the additional fee, and the revised timeline.

5.2 **No Change Order takes effect until signed by both parties.** Until then the Developer
continues under the existing scope.

5.3 Change Orders may extend delivery dates. The Client accepts that added scope moves the
timeline.

---

### 6. Acceptance

6.1 On completing a phase, the Developer notifies the Client and provides access for testing.

6.2 The Client has **[5] business days** to test and either accept in writing or provide a
single consolidated written list of defects — meaning failures to meet Schedule A, not
preferences or new requests.

6.3 **If the Client does not respond within [5] business days, the phase is deemed accepted.**

6.4 The Developer will correct valid defects within a reasonable period at no charge, after
which the phase is deemed accepted.

---

### 7. Client Responsibilities

7.1 The Client will provide, within [5] business days of request: business details, product
catalog data and pricing, images and branding assets, store hours, payment account details,
and any credentials needed for third-party integrations.

7.2 The Client will nominate **one** person authorised to give feedback, approve deliverables,
and sign Change Orders. Conflicting instructions from multiple people are not the Developer's
responsibility.

7.3 The Client is responsible for the accuracy and legality of all content and data it
supplies, and warrants it holds the rights to any material provided.

7.4 **Client delay extends deadlines.** Where the Client's delay in providing materials,
feedback, or payment causes the Developer to lose scheduled working time, delivery dates move
by at least the length of the delay. The Developer works [10] hours per week on this project;
a one-week delay is a one-week slip.

---

### 8. Timeline

8.1 The indicative schedule is in Schedule A.

8.2 Dates are estimates made in good faith, not guarantees. They assume timely payment,
timely Client feedback, and no scope changes.

8.3 Neither party is liable for delay caused by events outside its reasonable control,
including third-party service failure, illness, natural disaster, or loss of utilities or
internet.

---

### 9. Intellectual Property

9.1 **On receipt of all setup fees in full**, the Client owns: its data, its content and
branding assets, the client-specific configuration, and the custom-built features specific to
the Client's business as listed in Schedule A.

9.2 **The Developer retains all rights, title, and interest in** the underlying framework,
architecture, libraries, reusable components, generic modules, design patterns, and any
pre-existing or general-purpose code — including but not limited to authentication,
catalog, cart, checkout, order management, payment verification, delivery integration, store
settings, promotions, and point-of-sale modules. The Client receives a perpetual,
non-exclusive, non-transferable licence to use these as part of the delivered system.

9.3 **The Developer may reuse, adapt, license, and resell the components in 9.2 for any other
client or product, including competitors of the Client.** The Client acknowledges it is
purchasing a working system, not exclusive ownership of the underlying technology.

9.4 Until all setup fees are paid in full, all deliverables remain the Developer's property
and any licence to use them is suspended.

9.5 **Portfolio rights.** The Developer may describe this project, name the Client, and display
screenshots and metrics in a portfolio, case study, social media post, or video, unless the
Client objects in writing within [30] days of launch.

9.6 The Client agrees to provide, on request following successful launch, a written testimonial
and reasonable introductions or referrals. This is a good-faith obligation, not a condition of
payment.

---

### 10. Maintenance, Support, and Hosting (Phase 3)

10.1 The monthly fee under Clause 2 covers: application hosting and server management,
security patches and dependency updates, uptime monitoring, database backups
([daily], retained [7] days), bug fixes in delivered functionality, and support during the
hours in 10.3.

10.2 **Not covered:** new features, design changes, content or catalog updates, additional
integrations, staff retraining, data recovery caused by Client error, or issues arising from
Client-side hardware, internet, or third-party account problems. These are quoted separately
at ₱[  ] per hour.

10.3 Support hours are [Monday–Friday, 9:00–18:00 PHT]. Target first response:
[1] business day for standard issues, [4] hours for a total outage of the ordering system.
These are targets, not guaranteed service levels.

10.4 The monthly fee begins on launch and is billed in advance. Either party may terminate
Phase 3 on [30] days written notice.

10.5 **Non-payment [15] days past due entitles the Developer to suspend hosting and support
until the account is settled.** Suspension may take the Client's system offline. The Developer
is not liable for losses arising from suspension due to the Client's non-payment.

10.6 On termination of Phase 3 the Developer will provide the Client's data in a standard
exportable format ([CSV / SQL dump]) within [14] days, and will retain no obligation to host
or support the system thereafter. Migration assistance beyond data export is billable.

---

### 11. Warranty

11.1 For **[30] days** after acceptance of the final phase, the Developer will correct, at no
charge, defects that cause delivered functionality to fail to work as specified in Schedule A.

11.2 The warranty excludes: new features or changes of requirement, problems caused by Client
modification or misuse, third-party service failure, and issues arising from the Client's
hardware, network, or devices.

11.3 The system is delivered as-is beyond this express warranty. The Developer does not warrant
that the software will be error-free, uninterrupted, or fit for any purpose not stated in
Schedule A.

---

### 12. Data Privacy

12.1 The system will process personal data of the Client's customers. The **Client is the
personal information controller** and the **Developer is the personal information processor**
within the meaning of the Data Privacy Act of 2012 (RA 10173).

12.2 The Developer will process personal data only as needed to provide the services, apply
reasonable technical and organisational security measures, and not disclose personal data
except as required by law or to sub-processors necessary for the service (hosting, delivery,
payment).

12.3 The Client is responsible for its own privacy notice, obtaining any consents required
from its customers, and its own compliance obligations including any National Privacy
Commission registration.

12.4 Each party will notify the other without undue delay on becoming aware of a personal data
breach affecting the system.

---

### 13. Payment Verification Disclaimer

13.1 The Client acknowledges that where the system verifies payment from user-submitted
evidence — including screenshot or OCR-based verification of e-wallet transfers such as GCash —
such verification is **advisory and not conclusive**, and can be defeated by forged or altered
images.

13.2 The Client is solely responsible for confirming actual receipt of funds in its own
accounts before fulfilling any order, and accepts all risk of fraudulent, reversed, or
non-existent payments.

13.3 The Developer is not liable for any loss arising from payments that were not in fact
received.

---

### 14. Limitation of Liability

14.1 The Developer's total aggregate liability under this Agreement is limited to **the total
setup fees actually paid by the Client**.

14.2 Neither party is liable for indirect, incidental, special, or consequential loss,
including lost profits, lost sales, lost data, or business interruption.

14.3 Nothing in this clause limits liability for fraud, wilful misconduct, or any liability
that cannot lawfully be limited.

---

### 15. Confidentiality

15.1 Each party will keep the other's non-public business information confidential and use it
only for the purposes of this Agreement, for the term and [2] years after.

15.2 This does not apply to information that is public, independently developed, lawfully
received from a third party, or required to be disclosed by law.

15.3 Clause 15 does not restrict the Developer's rights under Clauses 9.2, 9.3, and 9.5.

---

### 16. Termination

16.1 Either party may terminate on [15] days written notice if the other materially breaches
this Agreement and fails to cure within that period.

16.2 The Client may terminate for convenience at any time. On such termination the Client pays
for all work performed to the date of termination, and all amounts already paid — including the
Discovery fee — are non-refundable.

16.3 On termination for any reason: Clause 9.4 applies until outstanding fees are settled, and
Clauses 9, 12, 14, and 15 survive.

---

### 17. General

17.1 **Independent contractor.** The Developer is an independent contractor, not an employee,
partner, or agent of the Client.

17.2 **Entire agreement.** This Agreement and its Schedules supersede all prior discussions,
quotes, proposals, and messages.

17.3 **Amendment.** Only in writing, signed by both parties.

17.4 **Assignment.** Neither party may assign this Agreement without the other's written
consent.

17.5 **Severability.** If any provision is unenforceable, the remainder stands.

17.6 **Governing law and venue.** This Agreement is governed by the laws of the Republic of
the Philippines. The parties submit to the exclusive jurisdiction of the courts of
[CITY], Philippines.

17.7 **Dispute resolution.** The parties will attempt good-faith negotiation for [30] days
before commencing proceedings.

---

### Signatures

**Developer**

Name: Eleomar Halaman
Signature: ______________________  Date: ____________

**Client**

Name: ______________________
Position: ______________________
For and on behalf of: **[CLIENT NAME]**
Signature: ______________________  Date: ____________

---

## Schedule A — Scope of Work

### System

[e.g. Online ordering system and point-of-sale for [CLIENT NAME], comprising a customer-facing
web storefront and a staff-facing admin console.]

### Phase 1 — Discovery

| # | Deliverable |
|---|---|
| 1.1 | Requirements workshop ([N] hours) |
| 1.2 | Walkthrough / dry run of the existing system |
| 1.3 | Written requirements document and confirmed v1 feature list |
| 1.4 | Confirmation of whether point-of-sale is in v1 or deferred to v2 |

### Phase 2 — Build

Included features — **delete what is not in v1:**

| # | Feature | In v1? |
|---|---|---|
| 2.1 | Customer accounts, login, registration | |
| 2.2 | Product catalog with categories and customisations | |
| 2.3 | Product image upload and management | |
| 2.4 | Cart, checkout, order placement | |
| 2.5 | Pickup scheduling and store hours / closures | |
| 2.6 | GCash payment submission and verification (see Clause 13) | |
| 2.7 | Lalamove delivery quoting and dispatch | |
| 2.8 | Promotions / discount codes | |
| 2.9 | FAQ management | |
| 2.10 | Admin console — live orders board, order history, settings | |
| 2.11 | **Point of sale — register, shift open/close, cash handling, receipts** | |
| 2.12 | **Inventory / stock tracking** | |
| 2.13 | **Sales reporting and dashboard** | |
| 2.14 | Production deployment — domain, SSL, server, database, backups | |
| 2.15 | System documentation and [N] staff training session(s) | |

### Phase 3 — Live

Hosting, maintenance, and support per Clause 10.

### Indicative Schedule

| Milestone | Target |
|---|---|
| Agreement signed, Discovery fee paid | [DATE] |
| Discovery complete, v1 scope locked | [DATE] |
| Build commences (50% received) | [DATE] |
| Build complete, handed for acceptance | [DATE] |
| Launch | [DATE] |

Assumes [10] developer hours per week, timely Client feedback per Clause 7, and no Change
Orders. See Clause 8.

---

## Notes to self — not part of the contract

- **Fill in every `[  ]` before sending.** A contract with visible placeholders reads as
  amateur and invites renegotiation of everything.
- **Get real quotes first.** The ₱ figures in [[GOALS.md]] are directional, not validated.
  Two or three local quotes for comparable work beats any number I reasoned my way to.
- **Clause 6.3 and 7.4 protect your 10 hours/week.** Client slowness is the most common way a
  side project dies. These make it their problem, not yours.
- **Clause 4 is aimed at you as much as them.** A revision cap is the contractual version of
  "stop polishing and ship."
- **Do not skip Clause 2.4.** No payment, no work. This is the one that stops the free-prototype
  pattern from repeating.
- **Ask the client 1.4 before quoting the build.** If POS defers to v2 you cut ~60h and ship
  roughly two months earlier.

# Perpetual Blue — BVI Crewed Charter Yacht

## Overview
- **Vessel:** 2013 Fountaine Pajot Sanya 57 Catamaran — "Perpetual Blue"
- **Configuration:** 6 cabins, crewed charter
- **Crew:** Captain **Sean Powell** + Chef **Elise** (Sean's partner)
- **Current ownership:** Florida LLC
- **Target season:** 2026/2027
- **Charter goal:** 15 charters minimum, up to 20
- **Website:** Already live · **Domain:** securing **www.perpetualbluebvi.com** (July 14, 2026)
- **Related file:** [Grenada Itinerary](grenada_itinerary.md)

---

## Business Systems — Banking, Cards, Accounting & CRM (decided July 14, 2026)

Stack for the **Florida LLC** (US operating entity):

- **Banking: Mercury (Free plan).** Free checking/savings, unlimited free cards, free bill pay, free QuickBooks sync. **Skip Mercury Plus (~$30–35/mo)** — its recurring/ACH-debit invoicing doesn't fit charter cash flow (paid by broker wire + one-off deposit/balance invoices). Upgrade only if direct-to-guest bookings scale.
- **Cards: Mercury IO business credit card** — $0 annual fee, 1.5% cash back, unlimited free employee cards (one each for owner, Captain Sean, Chef Elise) with per-card limits, native QuickBooks sync. Chose IO for ecosystem fit. Runner-up if a second issuer is wanted: **Chase Ink Business Cash** ($0 fee, 5% phone/internet/office + 2% gas/dining, big welcome bonus, Visa = better island acceptance).
- **Accounting: QuickBooks Online Simple Start (~$35/mo, ~$420/yr) from day one.** Deliberately paying vs. DIY books — clean tax return, accountant-openable file, audit trail, and it handles depreciation of the $400K refit / deferred deposits / loan principal-vs-interest that a bank feed can't.
- **CRM: to be built next.** Operational brain — charters → guests → brokers → commissions → deposits/payments — feeding clean categorized data into QuickBooks (and/or Mercury). Design principle: **CRM = system of record for operations; QuickBooks = statutory/tax books.** The charter calendar artifact is the first piece.

> ⚠️ Earlier (July 6) note: Mercury suits the FL LLC; **Wise Business** was recommended for a BVI BC. If the entity re-flags to a BVI BC, US cards (Mercury IO / Chase) require **keeping the US LLC** — reconcile card entity with the final corporate structure.

## Charter Calendar (visual artifact)
- **Live:** https://claude.ai/code/artifact/95f010e0-f26e-4f86-a9f7-873bfdde8fb0
- **Source (permanent):** `C:\Users\ducat\Projects\perpetual-blue\pb-calendar.html` · backed up to GitHub at `project_files/pb-calendar.html`. Redeploy: edit the file → Artifact tool with the same URL.
- 2026/2027 season, 7 charters (4 booked, 3 holds, **43 nights**). Nautical theme, theme-aware, monthly grids Dec 2026–Jun 2027. Header labeled "Perpetual Blue · BVI".
- **Half-day turnover tiles:** pickup day = afternoon-only diagonal (morning open, guest boards PM); departure day = morning-only diagonal (guest off ~10am, afternoon open for turnaround). Full nights solid. Legend has a "Turnover ½-day" swatch.

---

## Expenses & Receipts (feeds CRM + QuickBooks)

Running startup-expense log. **Plan:** once the business email (on `perpetualbluebvi.com`) is set up, user will grant Orion access to **scan receipts → categorize → push into the CRM and QuickBooks**. Until then, expenses are tracked here manually.

| Date | Item | Vendor | Amount | Category | Receipt |
|---|---|---|---|---|---|
| 2026-07-14 | Domain: **perpetualbluebvi.com** — 1-yr registration (declined GoDaddy privacy + MS365 upsells) | GoDaddy | **$13.19** ($12.99 + tax) | Startup · Web & Software | pending |
| 2026-07-28 | Captain Sean Powell — flight DOM (Dominica) → EIS (Tortola, BVI) | TBD | **$242.97** | Crew Travel | pending |
| 2026-07-28 | Elise McNabb — flight SJU (Puerto Rico) → EIS (Tortola, BVI) | TBD | **$108.97** | Crew Travel | pending |

**Running total: $365.13**

> Email: **Zoho Mail Free** (2 inboxes, set up 2026-07-14):
> - `charters@perpetualbluebvi.com` — **Nikunj** (owner; bookings/brokers)
> - `captainsean@perpetualbluebvi.com` — **Sean Powell** (captain)
>
> Chosen over GoDaddy's $71/yr MS365 upsell; free WHOIS privacy instead of the $13/yr add-on → ~$84/yr saved. Free plan = webmail + mobile app only (no IMAP/desktop-client unless upgraded to Mail Lite ~$1/user/mo).
>
> **Fully configured & verified 2026-07-14.** DNS in GoDaddy: MX (mx/mx2/mx3.zoho.com), SPF (GoDaddy `_spfm` merge that resolves to `include:zoho.com include:zohomail.com`), DKIM (selector `zmail`, live + verified), DMARC currently `p=none` with rua→charters@ (**bump to `p=quarantine` after monitoring**). Domain ownership verified by charters@. TODO: get Sean signed in (was "never signed in"), enable 2FA, mobile apps, draft email signatures.

---

## Corporate Structure — Decision Pending

### Advisor Recommendation
Open a **BVI Business Company (BC)**, re-flag vessel to BVI, and run charter operations out of BVI BC.

### BVI Business Company (BC)
- **Formation cost:** ~$450–$550 USD
- **Annual maintenance:** ~$450–$550/year (registered agent + government fee)
- **No BVI corporate tax, no capital gains tax, no withholding tax**
- **No VAT in BVI**
- Privacy: Directors/shareholders not publicly disclosed

### ⚠️ US Tax Obligations Still Apply (Do Not Skip)
As a US person owning a BVI BC:
- **Form 5471** — annual information return for US persons controlling a foreign corporation
- **FBAR** — if BVI BC has foreign bank accounts over $10,000
- **FATCA** — foreign financial accounts reporting
- **Subpart F / GILTI rules** — may apply depending on income structure
- BVI BC does NOT eliminate US tax obligations

**Action: Engage international tax attorney before forming BVI BC.**

### USVI LLC Alternative
- US territory — no foreign corporation reporting headaches
- Less favorable charter tax environment than BVI
- Good option if you want to keep everything domestic

---

## BVI Vessel Registration

- **Administered by:** VISMA (BVI Shipping and Maritime Authority) — bvimaritime.vg
- **Governing law:** Merchant Shipping (Amendment) Act, 2025 — expanded eligible jurisdictions to 75+ countries
- **US is reported to be on the eligible list** — confirm Florida LLC qualifies before initiating
- **Process:** Application to VISMA → vessel survey/inspection → delete existing US documentation

---

## BVI Charter Licensing (CRVL)

### Who Administers It
**BVI HM Customs** — Commercial Recreational Vessel Licencing Unit (CRVLU)
NOT the Tourist Board. NOT the FSC.

### Step 1: Charter Authorization Letter
Required exclusively for **Foreign-Based Charter Companies** (which a Florida LLC is).
- Apply at: bvi.gov.vg/services/charter-authorization
- This must be obtained before the CRVL

### Step 2: Commercial Recreational Vessel License (CRVL)
For a foreign-based vessel under 115ft, two tiers:

| Tier | Cost | Cap |
|---|---|---|
| Framework B — Restricted | $7,500/yr or $1,200/entry (max 7 entries) | 7 charters max |
| **Framework A — Unrestricted** | **$24,000/year flat** | Unlimited |

**For 15–20 charters → Framework A required = $24,000/year**

Alternative Framework A pricing: $7,500 + $2,100 per charter after 7 charters (confirm which applies)

Effective date of current fee structure: June 1, 2025 (Commercial Recreational Vessels Licensing Amendment Act No. 13 of 2025)

### 60-Day Continuous Stay Rule ⚠️
Foreign-flagged commercial vessels cannot remain in BVI waters more than **60 continuous days** without additional permits. Extensions available via three consecutive 30-day off-charter permits. Plan season schedule around this.

### CRVL Application Documents Required
- Charter Authorization Letter (approved)
- Proof of commercial charter insurance (covering BVI waters, oil pollution, salvage)
- Vessel documentation

---

## Crew Certifications

### Captain
**Option A: MCA OOW Yacht Certificate**
- 36 months total onboard yacht service since age 16
- 365 days on vessels 15m+ with at least 250 days actual sea service
- Valid ENG1 medical certificate (hard requirement)
- Unlimited geographic scope on yachts under 3,000 GT

**Option B: RYA Yachtmaster Ocean + Commercial Endorsement** ✅ Most common in BVI
- RYA Yachtmaster **Ocean** (not Offshore — Offshore is limited to 150 miles from safe haven)
- Commercial Endorsement requires:
  - Valid ENG1 or ML5 medical certificate
  - Professional Practices & Responsibilities (PPR) course
  - Sea survival certificate
  - SRC marine radio certificate
  - Original Certificate of Competence

### First Mate / Chef
- STCW Basic Safety Training (best practice — legal requirement not confirmed for this vessel size)
- Food handling certification (ServSafe or equivalent)
- CPR / First Aid
- **Action: Confirm exact requirements with CRVLU or BVI maritime attorney**

---

## Insurance

### Hard Rule
Charter use is a **hard exclusion on standard pleasure-use policies**. Current pleasure policy voids the moment paying guests board. A **separate commercial charter policy is mandatory** before first charter.

### What BVI CRVL Requires
- Coverage for vessel operations in BVI waters
- Oil pollution coverage
- Salvage coverage

### What to Get
- **Hull & Machinery (H&M)** — physical vessel damage
- **P&I (Protection & Indemnity)** — third-party liability (guest injuries, crew, other vessels)
- Commercial charter endorsement on both

### Recommended Insurers to Quote
- Pantaenius (major Caribbean yacht insurer)
- Suncoast Insurance
- Anchor Marine
- Markel / BOA (specialist charter underwriters)

*Note: The commonly cited "3–5% of hull value" range was not confirmed in research. Get actual quotes.*

---

## BVI Charter Yacht Show

| Detail | Info |
|---|---|
| **Venue** | Nanny Cay Resort & Marina, Tortola (+ Virgin Gorda Yacht Harbour) |
| **Organizer** | Charter Yacht Society BVI — crewedyachtsbvi.com |
| **2026 Dates** | November 10–13, 2026 (confirm with organizer) |
| **Broker attendance** | 140 (2024), 173 (2025) |
| **Why attend** | Single highest-leverage event for self-managed operators in the Caribbean |

### How to Register as Exhibitor
Contact crewedyachtsbvi.com directly. Register Perpetual Blue as a Crewed Charter Vessel participant.
- Need: CRVL (or in-process), charter specs/brochure, captain available for boat tours
- Slip fees vary by vessel size — confirm with organizer

---

## Self-Management Operations

### What the Management Company Was Doing (Now Your Job)

| Function | Details |
|---|---|
| Charter broker relationships | 15–20% commission on base charter fee (industry standard) |
| Directory listings | VIPCA, CharterWorld, YachtCharterFleet, YATCO, Noonsite |
| Charter contract | Base rate + APA (30–35% of charter fee for fuel, food, port fees) |
| Booking & payment | Wire transfers standard; direct broker booking |
| Guest provisioning | APA covers this — Rite-Way Food Markets (Tortola) delivers to dock |
| Maintenance logs | Required for CRVL renewal |
| Compliance renewal | CRVL annual, insurance annual, crew cert tracking, vessel survey |
| Customs/Immigration | Clear in at every BVI port of entry; track guest cruising fees |

### Charter Broker Commission Structure
- 15% to broker on **base charter fee** — non-negotiable industry standard
- APA (Advance Provisioning Allowance) flows through owner — not commissionable
- Collect charter fee + APA → pay broker 15% of charter fee

### Getting on Broker Lists
1. Attend BVI Charter Yacht Show (primary path)
2. **VIPCA membership** — directory listing + credibility
3. Direct outreach to top BVI charter brokers with spec sheet + pro photos
4. Quality photography is non-negotiable — brokers forward photos to clients

---

## Budget & Fees (Annual Operating Costs — Known)

| Item | Cost |
|---|---|
| CRVL Framework A | $24,000/year |
| BVI BC formation (one-time) | ~$500 |
| BVI BC annual maintenance | ~$500/year |
| Commercial charter insurance | TBD — get quotes |
| VIPCA membership | TBD |
| BVI Yacht Show slip fee | TBD |

---

## Charter Operations Budget
- **Template source:** Friend who owns "Deep Blue" — self-managed crewed charter, same setup
- **Next step:** Get template from friend → build out Perpetual Blue version with actual numbers
- Will need: annual costs vs. projected revenue at 15 charters vs. 20 charters

---

## Open Questions / Action Items

- [x] Hire regulatory agent for BVI registration — **ABM Group hired (July 2026)**
- [ ] Confirm ABM is handling CRVL + Charter Authorization Letter (not just company + vessel registration)
- [ ] Open **Wise Business** account for BVI BC banking (primary); add Butterfield Bank BVI as secondary
- [ ] Engage international tax attorney re: BVI BC structure (Form 5471, FBAR, GILTI)
- [ ] Confirm Florida LLC eligibility on VISMA jurisdictions list
- [ ] Get commercial charter insurance quotes (Pantaenius, Suncoast, Anchor Marine, Markel)
- [ ] Confirm captain holds RYA Yachtmaster Ocean + Commercial Endorsement + ENG1
- [ ] Confirm STCW requirements for first mate/chef with CRVLU
- [ ] Get current BVI cruising permit fees + customs/immigration passenger fees for budget
- [ ] Contact O'Neal Webster (BVI) or similar maritime attorney for Charter Authorization Letter process
- [ ] Register for BVI Charter Yacht Show (November 2026) — crewedyachtsbvi.com
- [ ] Join VIPCA
- [ ] Commission professional photos + spec sheet for broker outreach
- [ ] Set up provisioning account with Rite-Way Food Markets, Tortola
- [ ] Order guest gift mugs — Kodiak Wholesale 12oz stemless insulated wine tumblers, navy powder coat, laser engraved Perpetual Blue logo. ~$10–12/unit, 150/season (10 per charter x 15 charters) = ~$1,500–$1,800/season. Optional: add guest first name on back. URL: kodiak-wholesale.com/products/bulk-custom-wine-cups-12-oz-engraved-wholesale
- [ ] Build broker gift program — YETI or quality 20oz tumbler + handwritten note from owner to every broker who books Perpetual Blue. Start with Jonna Duvernoy (AMWAX Prime — jonna@amwaxprime.com, +1 407 512 0552)

---

## Key Contacts & Resources

| Resource | URL / Contact |
|---|---|
| BVI CRVL Application | bvi.gov.vg/content/commercial-recreational-licensing |
| Charter Authorization Letter | bvi.gov.vg/services/charter-authorization |
| VISMA (vessel registration) | bvimaritime.vg |
| O'Neal Webster (BVI law) | onealwebster.com |
| VIPCA | vipca.org |
| Charter Yacht Society BVI (show) | crewedyachtsbvi.com |
| Rite-Way Provisioning (Tortola) | riteway.vg |
| CharterWorld listing | charterworld.com |
| YATCO listing | yatco.com |

---

## Research Notes
- Research conducted: June 2026 (110 agents, 27 sources, 25 claims adversarially verified)
- BVI charter law overhauled June 1, 2025 — re-verify fees annually
- Primary legal source: O'Neal Webster BVI analysis of Commercial Recreational Vessels Licensing Amendment Act No. 13 of 2025

---

## Session Log

### Session: July 8, 2026
- Built **Charter Log spreadsheet** — `C:\Users\ducat\Projects\Perpetual_Blue_Charter_Log.xlsx` — 3 sheets: Charter Log (30 rows, 25 columns, all formulas wired), Season Summary (SUMIF/COUNTIF by year 2027/2028/2029), Notes & Legend. Pre-loaded with Lacie Clark charter (PYC PPB 052727, May 27–Jun 3 2027, $26,800, 8 guests, BVI). Built in Node.js using ExcelJS.
- Researched **Jonna Duvernoy** — confirmed she works at **AMWAX Prime Yacht Charters** (jonna@amwaxprime.com, +1 407 512 0552). AMWAX Prime is an active broker at the BVI Charter Yacht Show.
- Decided to build **broker relationship gift program** — quality 20oz tumbler + handwritten note from owner to every broker who has booked Perpetual Blue. Starting with Jonna.
- Decided on **guest gift program** — set of 10 x **Kodiak Wholesale 12oz stemless insulated wine tumblers**, navy powder coat, Perpetual Blue logo laser engraved. ~$10–12/unit, ~150/season. Optional: guest first name on back. Order sample first. kodiak-wholesale.com/products/bulk-custom-wine-cups-12-oz-engraved-wholesale
- Kodiak Wholesale handles mug + engraving in-house, ships in 1–3 business days, no setup fees, free digital proof, $250 minimum.

### Session: July 6, 2026
- Hired **ABM Group** (abm.vg) to handle regulatory work for Perpetual Blue — BVI vessel registration, BVI BC formation, and basing the vessel in BVI
- Researched ABM reputation: Licensed by BVI FSC (CITL21007), physical offices in Tortola + St. John USVI, 12+ years in operation, 40 years team experience, reviews 80–100% across all verified platforms, customers praise post-sale responsiveness. **Verdict: Reputable — good choice.**
- ABM handles: BVI vessel registration, BVI BC formation, mortgage registration/discharge, tonnage certification, safety surveys
- Action item: Confirm ABM is also covering BVI Charter Yacht License (CRVL) and Charter Authorization Letter — those are separate from company formation and vessel registration
- Researched banking for BVI LLC/BC:
  - **Mercury** (mercury.com) — best for FL LLC; won't work as well for BVI BC
  - **Wise Business** (wise.com/us/business) — top recommendation for BVI BC; accepts non-US entities, real USD account details, multi-currency, fully online
  - **Airwallex** — strong alternative to Wise for international entities
  - **Butterfield Bank** (Road Town, Tortola) — most reputable local BVI bank, serves BVI BC entities, good for charter community relationships
  - **Recommendation:** Open Wise Business as primary operating account; add Butterfield Bank once on the ground in BVI
- IRS Form 843 / Kwong v. United States: Confirmed general mailbox rule (IRC §7502) applies — postmark by July 10 deadline counts. Recommend USPS Certified Mail or approved private carrier for proof. Advised to call IRS to confirm if July 10 is postmark vs. received-by for this specific Kwong-related claim.

### Session: June 15, 2026
- Finalized Grenada itinerary: Aug 12–21 2026 · 10 days / 9 nights · 6 guests + Captain Sean Powell
- Route: Grand Anse (2 nights) → Bathway/Levera NE Grenada → Tyrrel Bay Carriacou → Sandy Island + Underwater Sculptures → Salt Whistle Bay Mayreau → Tobago Cays (2 nights) → Tyrrel Bay → Grand Anse west coast run
- Built guest-shareable HTML itinerary page: `C:\Users\ducat\Projects\grenada-itinerary\index.html` — drag folder to drop.netlify.com for instant public link
- Updated P&L builder (`build-perpetual-blue-pl.js`) to 3 scenario columns: B=10 charters, C=15 charters, D=20 charters — all formulas independent per column
- Loaded real numbers from friend's Deep Blue template into P&L — all 36 expense line items populated across 3 scenarios
- Added Mortgage line item ($36K flat across all scenarios)
- Confirmed BVI charters are all-inclusive (no APA structure)
- Updated expense numbers (round 2): Advertising +$5K, Exterior cleaning revised down, Customs up, Provisions revised, R&M up to $45K/$50K/$60K, Trash revised
- Fixed formula bug: Total Income formula changed from SUM(B7:B9) to B7-B9 so broker fees are entered as positive and subtracted (Google Sheets was reading stale cached results)
- Final P&L numbers (round 3): Charter Income $265K/$397.5K/$530K · Broker Fees $39.75K/$59.6K/$79.5K · R&M revised to $40K/$45K/$50K
- Net Income: ($45,800) at 10 charters · $9,525 at 15 charters · $70,800 at 20 charters
- Break-even between 15–20 charters; 15 charters is marginally profitable
- Output: `C:\Users\ducat\Projects\Perpetual_Blue_PnL.xlsx`

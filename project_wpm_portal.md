---
name: project-wpm-portal
description: "WPM Property Management Portal — custom CRM to replace Buildium/Rentec for long-term residential. First property: Snow Mountain Ranch (43 staff units). Build starts when SMR contract signed."
metadata: 
  node_type: memory
  type: project
  originSessionId: cb8dbb3a-425a-4e53-a5c9-b7ae67499936
  modified: 2026-08-29T23:02:25.843Z
---

# WPM Property Management Portal

**Status:** Planned — waiting on Snow Mountain Ranch management contract to kick off build.
**Build time:** 18–22 hours with Orion across sessions.
**Full plan file:** `C:\Users\ducat\Projects\wpm-portal-plan.md`

## Why
Replace Buildium/AppFolio/Rentec ($45–$280/mo) with an owned system. At 200 units saves $600–3,000/month vs. SaaS. Build once, own forever.

## ⭐ Requirements locked 2026-08-29 (supersede the older plan file where they conflict)

**Ownership & parties — the structural facts everything else depends on:**
- **YMCA owns the properties, all but ONE** → at least **two owner entities from day one**. Multi-owner must be in v1, not retrofitted.
- **The lease is between YMCA and the tenant.** WPM is the **managing agent, not the lessor.**
- **YMCA does not collect rent today — WPM will collect it.** So WPM holds other people's money → **trust/escrow accounting is in scope**, with per-owner sub-ledgers and three-way reconciliation.
- ⚠️ **OPEN GO/NO-GO:** Nick says "we have a real estate agent on payroll so DORA compliance is doable." Colorado trust accounts are likely the duty of an **employing broker**, not a licensed associate. **Confirm WPM has an employing broker of record** — if not, this blocks the whole payment module.
- **YMCA is both employer and landlord** → check whether lease termination can follow employment termination in CO.

**Units — the central modeling problem:**
- **~42 rentable units, not 42 properties.** A 3-bedroom house may be leased **by the room** to three unrelated tenants: one property, three leases, three payment streams, shared kitchen/baths. (Older note said "43 units" and missed this entirely.)
- Model must be **owner → property → unit → room**. If it can't express a **partial turnover** (one room turning while two tenants remain), it's wrong.

**Payments:**
- ✅ **Bank/ACH ONLY.** Credit cards were requested then **explicitly retracted**.
- ❌ **NO payroll deduction** — explicitly ruled out (don't re-propose it; it was raised and rejected).

**Roles — four, with three isolation boundaries:**
1. **Tenant** — own unit only; submit maintenance, see status, pay
2. **WPM admin** — everything
3. **YMCA owner** — scoped visibility; **must NOT see the one non-YMCA property**; may need sub-roles (HR / finance / facilities)
4. **Maintenance staff** — work orders + unit access + tenant contact; **NO** rent, ledgers, leases, banking

**Maintenance — decided to BUILD, not hand off:**
- SMR already runs its own system for **~150 other units** and staffs **24/7 on-call**. Initially the plan was push-and-handoff.
- **Reversed 2026-08-29: build it here.** SMR maintenance staff (primarily YMCA employees, occasionally WPM fill-ins) **log into WPM's CRM** so the tenant sees status and can communicate in one place.
- **Why it works:** unit sets are **disjoint** — WPM's CRM is system of record for its ~42 units, SMR's system keeps their other ~150. No sync, no duplicate tickets.
- **The real risk is adoption, not code:** staff context-switching between two apps. A request rotting while the tenant watches a stale status is *worse* than the handoff model. Mitigations: mobile-first, deep links from notifications, staleness/SLA flags in the admin queue — plus an explicit operational agreement with SMR.
- **Notifications go out through SMR's EXISTING communication tools; updates happen by logging into WPM's CRM.** Build a channel-agnostic notification layer with pluggable adapters.
- ⚠️ Auth friction on deep links is where adoption lives or dies — narrow maintenance permissions are what make a more convenient auth posture defensible.

**Turnover & projects — two more modules (turnover was missing from the original plan):**
- **Turnover/make-ready is a WPM contractual duty between every tenancy** — move-out inspection → clean → repairs → move-in inspection → ready. **Unit readiness is a calendar state**: vacant ≠ vacant-and-rentable.
- **Photo-evidence inspections are a legal artifact**, not a checklist — they're the entire basis for deposit deductions. CO wrongful withholding may carry **treble damages + attorney fees** (being verified).
- Split **tenant-billed damage** vs **owner-billed normal wear** — two different money flows.
- **Remodels / larger projects**: occasional, owner-directed. Needs **YMCA approval workflow before spend**, change orders, vendor management, budget vs. actual, owner billing.
- Three distinct kinds of work now: routine maintenance, turnover (recurring), projects (occasional). Don't collapse them into one concept.

**Research in flight (4 background agents, 2026-08-29)** → `C:\Users\ducat\Projects\smr-crm\`: `research-report.md` (architecture), `research-legal-colorado.md`, `research-payments-ach.md`, `research-competitors.md`. The competitive agent was briefed to genuinely stress-test build-vs-buy, not to confirm the prior "build custom" call — that decision predates trust accounting, NACHA returns, and inspections being in scope.

**Open questions for the client:** employing broker of record? who owns the non-YMCA property? what maintenance system + comms tools does SMR use (Teams/Slack/email/text)? does YMCA run M365 or Google Workspace (drives SSO)? are maintenance staff YMCA employees, WPM, or vendors?

⚠️ **A2P 10DLC registration** (business SMS) is a go-live prerequisite with real lead time — sits alongside Stripe/Plaid approval windows. The older plan didn't account for it.

## ⚖️ Legal findings 2026-08-29 (4 research agents · full detail in `Projects\smr-crm\`)

**Decision brief artifact:** https://claude.ai/code/artifact/ee156dc3-c429-47e8-bdc4-4d7b22aaa5bb (source `Projects\smr-crm\decision-brief.html` — republish that path to update)

**⭐ THE STRUCTURE THAT WON — rent goes directly to a YMCA-controlled account; WPM has VIEW-ONLY access and never takes custody.**
- **Trust/escrow accounting: ELIMINATED.** Every CREC Chapter 5 rule triggers on **custody, not agency** ("receives," "accepted," "holding," "collected"). Rule 5.4 is near-dispositive: *"If the Brokerage Firm is not in possession of Money Belonging to Others, there is no obligation to maintain a separate Trust or Escrow Account."* View-only is not custody — the rules never use "control" as the test. Journals, beneficiary ledgers, monthly three-way reconciliation, Rule 5.9 diversion risk and the 60-day clawback all fall away with it.
- 🚨 **THE TRAP: never net the management fee out of rent. Invoice YMCA separately.** Netting = WPM handled the money = **reinstates all of Chapter 5.** Put it in the management agreement explicitly.
- ⚠️ **The one non-YMCA property could reinstate the whole trust apparatus** if WPM collects its rent. Route it the same way.
- **YMCA becomes ACH Originator.** WPM is a **Third-Party Service Provider** *only if YMCA signs the processor agreement*; if WPM originates through its own processor it becomes a **Third-Party Sender** — registration + annual ACH audit. Decided by whose name is on the contract.

**🔴 LICENSING — the blocker that survives everything.**
- `C.R.S. § 12-10-201(6)` triggers on renting/leasing "on behalf of the owner" **for compensation. Money appears nowhere in the definition.** Direct deposit does NOT fix this. The separate invoice that protects the trust exemption *is* the compensation that triggers licensure.
- **All 16 exemptions in § 12-10-201(6)(b) tested individually — ZERO apply.** WPM fails two structural gates: it's **a corporate entity, not a natural person**, and it **neither owns the property nor is the owner's employee.** No nonprofit and no employee-housing exemption exist in Title 12.
- **Unenforceability is VERIFIED, not inference:** *Benham v. Heyde*, 122 Colo. 233, 221 P.2d 1078 (1950), reaffirmed *Amedeus Corp. v. McAllister*, 232 P.3d 107 (Colo. App. 2009) — an agreement to compensate an unlicensed broker is **illegal and unenforceable**. Common law, settled. WPM could manage a year and recover nothing. Being wrong is **retroactive and incurable**.
- **Licensure costs only ~$1,500–$3,000, weeks-to-months** — less than one month's management fee. Gating item: **does the agent on payroll have 2 years' experience** for the Employing Broker upgrade?

**⭐ THE MOST PROMISING ROUTE — Commission Position 24 (formerly CP-42), *Apartment Building or Complex Management*.** Lists duties an **unlicensed on-site manager** may perform, and WPM's reduced scope maps onto it almost exactly: *showing available units · quoting the rental price set by the owner · acting as scrivener completing predetermined lease terms on preprinted forms · collecting applications · scheduling maintenance · collecting rent.* Corroborated by **Rule 1.61** (verbatim). **These acts are NOT inherently licensed activity — Nick's instinct was right.**
- **But the obstacle is STATUS, not task.** The exemption runs to *"a regularly salaried employee of an owner."* A fee-paid third-party company is neither a Brokerage Firm nor an Unlicensed On-Site Manager.
- **The fix is small:** put the *specific staff* who show units and complete leases on **YMCA's payroll**. Same people, same work — only the employment wrapper changes. WPM keeps cleaning, inspections, maintenance, admin under contract.
- **Showings line = passive presentation vs. active offering.** Unlocking, pointing out features, stating YMCA's fixed rent = fine. Marketing, soliciting, or *"we could probably do better on the rent"* = licensed activity.
- **UPL, counterintuitive:** `§ 12-10-403` / *Conway-Bogue* (1957) carve a UPL exception **for licensed brokers**. Unlicensed, WPM loses that shelter and falls back on the narrower scrivener doctrine. Fine for typing a name and date; risky if selecting forms or explaining terms. **Never invoice a line item for "lease preparation."**
- ✅ **YMCA's authorised signatory should execute every lease** — cleanest fix, adopt regardless of route.
- ⚠️ **Substance governs over paperwork.** `§ 12-10-217(1)` reaches anyone who *"assumes to act in the capacity of a licensee."* The failure mode isn't a deliberate breach — it's a helpful employee on a Tuesday.

**🔴 THE STING NOBODY EXPECTED — getting licensed taxes the EXISTING vacation-rental business.**
- **Short-term rentals need NO licence.** Commission Position 12: an STR is *"a license to use property and is not considered Real Estate Brokerage Services."* WPM's current STR business is fine. (Test is lease-vs-licence character, **not** the 30-day line.)
- **BUT `§ 12-10-217(1)(h)` reaches others' money *"whether acting as real estate brokers or otherwise,"* and Rule 5.11 expressly names "Guest deposits for short term rentals."** Both bind **licensees only**. So **getting licensed for SMR pulls WPM's STR guest deposits into CREC trust accounting and audit — currently unregulated.** The direct-deposit fix removed trust accounting from SMR and it reappears on the vacation-rental side. **Price this into the SMR decision.**

**⚠️ Sourcing caveat:** every Colorado DRE PDF returned **403**. CP-12 and CP-24 quotes come from search extracts corroborated by verified Rule 1.61 / 5.11 text. **Read CP-12 and CP-24 directly before relying on them.**

**▶ NEXT STEP — written inquiry to the Division of Real Estate**, drafted at `Projects\smr-crm\dre-inquiry-letter.md`. Cheap, fast, close to dispositive. Asks: do these facts require a licence · does CP-24 reach a third-party company or only salaried employees · would putting showing/leasing staff on YMCA's payroll fix it · where's the line on showings · is view-only custody · **and (optional §4, Nick's call) does licensing pull STR guest deposits into trust accounting.** Have a Colorado attorney review before sending.

**Other locked facts:** Reg E `12 CFR § 1005.10(d)` — varying-amount debits need **10 days' advance notice** (now YMCA's duty as Originator). **HB25-1249 effective 2026-01-01** rewrote the deposit statute: trigger moved from "willful" to **"wrongful,"** three mechanical failures now deemed wrongful with **no scienter** → near strict-liability treble damages + one-way attorney fees, so **photo-evidence inspections are the highest-value module.** **Employer-provided housing is carved out of the 2024 just-cause eviction law** (§ 38-12-1302(1)(d)) with a **3-day cure vs 10** — but it attaches **per lease, not per property**, and a retiree/contractor/seasonal-stayed-on silently converts to full protection (**3× monthly rent or $5,000** liability). **Colorado Privacy Act does not apply.** **10-year carpet rule:** carpet 10+ years old = no carpet deduction at all. **A2P 10DLC**: carriers block unregistered traffic outright since Feb 2025, no bounce; 3–5 week lead time. **Zero Colorado authority exists** on apportioning by-the-room shared-space damage — pure lease-drafting problem.

**Build estimate, revised:** full custom w/ trust accounting 407–585 contractor hrs; v1 with money bought 220–310; **v1 direct-deposit ~120–180 contractor hrs ≈ 40–70 of Nick's engaged agent-assisted hours (~4–7 weeks at 10 hrs/wk).** Parallel agents don't divide evenly — ~65% mechanical parallelises 4–6×, ~35% judgment only 1.5–2×; review capacity is the real bottleneck. **The old "$600–3,000/mo saved" justification is dead** — Yardi Breeze is ~$100/mo at 42 units and **Stripe ACH fees (~$210/mo) exceed the SaaS being avoided.** Build on capability, not cost.

## First Property
- **Snow Mountain Ranch** (YMCA of the Rockies, Granby CO)
- 43 staff units, 12-month leases, individual employee payments
- Long-term play: staff/employee housing is a niche nobody in PM software serves properly

## Tech Stack
- Next.js + Supabase + Tailwind (same as Condo Assistant)
- Stripe + Plaid for ACH payments
- Twilio for SMS notifications
- Vercel hosting
- Monthly cost: ~$25–45 + Stripe fees (0.8% per ACH, max $5)

## 5 Phases
1. **Foundation** (4–6 hrs) — Admin dashboard, tenant logins, lease/unit management
2. **Payments** (4–6 hrs) — Stripe + Plaid ACH, autopay, SMS reminders via Twilio
3. **Maintenance** (2–3 hrs) — Request submission with photos, admin queue, calendar, recurring tasks
4. **Bookkeeping** (4–5 hrs) — Income/expense tracking, rent roll, P&L, owner statements, custom reports (CSV/PDF)
5. **Staff Housing Features** (2–3 hrs) — Employer split billing, employment-linked leases, payroll deduction CSV export, employer dashboard

## Key Differentiators vs. Every Competitor
- SMS rent reminders (AppFolio, Buildium, Rentec all lack this)
- Flexible custom reports (biggest complaint about all competitors)
- Maintenance calendar + recurring tasks (all competitors miss this)
- Staff housing / employer split billing (NOBODY builds this — real gap in resort/hospitality markets)
- Payroll deduction export for HR
- Employment-status-linked lease logic

## Go-Live Prerequisites
- Stripe merchant account (apply immediately when contract signed — 3–7 day approval)
- Plaid API access (same timeline)
- Twilio phone number ($1/month)
- SMR management agreement signed

**Why:** User recognized that PM CRM space is crowded, Condo Assistant is the unique product. WPM Portal is an internal tool to save $600–3,000/month at scale, with staff housing as a real niche differentiator if it ever becomes its own product.

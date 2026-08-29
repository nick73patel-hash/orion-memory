---
name: project-wpm-portal
description: "WPM Property Management Portal — custom CRM to replace Buildium/Rentec for long-term residential. First property: Snow Mountain Ranch (43 staff units). Build starts when SMR contract signed."
metadata: 
  node_type: memory
  type: project
  originSessionId: cb8dbb3a-425a-4e53-a5c9-b7ae67499936
  modified: 2026-08-29T20:41:20.580Z
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

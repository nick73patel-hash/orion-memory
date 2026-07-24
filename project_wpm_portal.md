---
name: project-wpm-portal
description: "WPM Property Management Portal — custom CRM to replace Buildium/Rentec for long-term residential. First property: Snow Mountain Ranch (43 staff units). Build starts when SMR contract signed."
metadata: 
  node_type: memory
  type: project
  originSessionId: cb8dbb3a-425a-4e53-a5c9-b7ae67499936
---

# WPM Property Management Portal

**Status:** Planned — waiting on Snow Mountain Ranch management contract to kick off build.
**Build time:** 18–22 hours with Orion across sessions.
**Full plan file:** `C:\Users\ducat\Projects\wpm-portal-plan.md`

## Why
Replace Buildium/AppFolio/Rentec ($45–$280/mo) with an owned system. At 200 units saves $600–3,000/month vs. SaaS. Build once, own forever.

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

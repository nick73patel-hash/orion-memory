---
name: condo-assistant-project
description: "AI virtual concierge web app for Winter Park Management vacation rentals — project location, stack, and status"
metadata: 
  node_type: memory
  type: project
  originSessionId: 2f508bd7-8698-45f7-9fcc-37235f88587b
---

Next.js 15 + Supabase + Claude API app at `C:\Users\ducat\Documents\condo-assistant`.

Built as an AI concierge for **Winter Park Management** (Winter Park, CO). Guests get a time-limited link and chat with an AI about their stay.

**Partners / Equity (confirmed July 11, 2026):**
- **Alex** — partner
- **Rob** — 10% equity, security & development expert; oversees and reviews Orion's (Claude Code) work

**Why:** Property management company needed a way to answer guest questions 24/7 without staff intervention. Unanswered questions email the admin automatically.

**How to apply:** When opening this project, always switch working directory to `C:\Users\ducat\Documents\condo-assistant`. CLAUDE.md in the project root has full architecture details.

Session log (what was built, bugs fixed, pending items): `C:\Users\ducat\Documents\condo-assistant\.claude\session-log.md` — read this at the start of every session.

Key facts:
- 3-tier knowledge hierarchy: Company → Building → Unit (all searched via RAG on each guest message)
- Guest chat at `/guest/[token]` — voice input + TTS enabled
- Admin panel at `/admin` — manage company/buildings/units/knowledge/guest links
- Deploy target: Vercel + `assistant.winterparkmanagement.com`
- Last active development: April 15, 2026

**SaaS Layer (in progress):**
Two target segments:
1. **Individual owners** (self-managing) — $40/month, target 5,000 clients = $2.4M ARR
2. **Property management companies** — $99–$499/month tiered, target 200 clients = ~$480K ARR
Combined target: ~$2.88M ARR

Marketing site to be built as a landing page within the existing Next.js app (separate from the app itself). Domain TBD. No product name chosen yet beyond "Condo Assistant".

**Pricing model (updated July 11, 2026 — supersedes old $40/mo flat):**
Per-unit/month, two products: Condo Assistant $15, Auto Email $15, Bundle (both) $25 base. Bundle volume tiers: 1–2 $25 · 3–9 $22 · 10+ $20 per unit. **$500/mo cap per client (up to 100 units); 100+ = custom.** Cap smooths the curve (no cliff) — bill rises with units then plateaus at $500; big clients get deep effective discount (75 units ≈ $6.67/unit).

**Client distribution (user's numbers, July 11):** 4,000 clients @ 1–2 units · 500 @ 3–9 · 300 @ 10–19 · 200 @ 20–49 · 50 @ 50–100 (seeded "rest") = 5,050 clients, 22,150 units.

**Strategy (user, July 11):** Priority = under-5-unit owners (most profitable/unit, biggest segment). Large PM companies priced aggressively (capped) on purpose — their adoption + referrals make the small profitable owners cheaper/easier to land (channel play).

**Financial model + plan built (July 11, 2026) — to show Alex:**
- `project_files/Condo_Assistant_SaaS_Model.xlsx` — 5 tabs (Pricing, Revenue Model, Unit Economics, Rollout Ramp, Assumptions). At full ~5,050-client penetration: 22,150 units, ~$368K/mo, **~$4.42M ARR**, 78% gross margin, LTV:CAC 12.7x. All inputs editable/illustrative.
- `project_files/Condo_Assistant_Business_Plan.docx` — Google Docs-ready plan for Alex: product, pricing (per-unit + $500 cap), revenue, unit economics, 3 rollout options (A referral-led / B paid / C channel-partner), phased ramp (Y1 500 → Y2 2,000 → Y3 5,050 clients).
- Rollout recommendation: Option A (small owners via referrals, near-zero CAC) Y1 → layer C (land PM cos who credential small owners) Y2 → add B (paid) Y3.
- Everything a first-pass guess — replace estimates with real data as it comes in.

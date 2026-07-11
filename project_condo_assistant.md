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

**Partners / Equity (as of July 11, 2026):**
- **Alex** — partner
- **Rob** — 10% equity, security & development expert; oversees and reviews Orion's (Claude Code) work *(tentative — "maybe" per user, confirm)*
- *(Eliot — previously listed as a partner; confirm current involvement)*

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

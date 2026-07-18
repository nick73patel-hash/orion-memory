# Orion Session Log

Running log of work sessions. Newest first. Project-specific detail lives in each `project_*.md`; this is the chronological overview.

---

## Session: July 18, 2026

**Theme:** Master to-do list built + Perpetual Blue structure, licensing, and compliance deep-dive.

### 📋 Master To-Do List
- Built a full interactive to-do artifact (45 items, 6 categories) from scratch — pulled from memory + session log carry-forwards + user's paper notes.
- Categories: Perpetual Blue & Charter (31), Condo Assistant (2), Winter Park Management (1), Financial & Tax (2), Personal (7), Tech & Dev (2).
- Artifact lives at: https://claude.ai/code/artifact/1149007f-7c0c-4bb8-a608-af4970f4803f — checkboxes tick off live, badge counts update.
- Deleted 9 completed/irrelevant items from prior list. Added ~30 new items from paper notes.
- Notes panel at bottom of artifact: PB original structure record + new structure + verified CRVL fee table.
- Placeholder credentials file created: `pb_licenses.md` — ready to fill in as numbers are obtained.

### ⛵ Perpetual Blue — Structure & Compliance
- **Current:** Florida LLC · BVI flag · Foreign-based (on record)
- **Transitioning to (Nov 1):** BVI BC (LLC) · BVI flag · BVI-based · Work permits in process for South African captain (Sean Powell) + American chef (Elise)
- **Owner status:** Nick = owner in transit — no BVI work permit required
- **CRVL license fees (verified from official BVI amendment docs):**
  - BVI-based, 50–60 ft (PB): **$1,600/year**
  - Foreign-based restricted: $7,500 + $1,200/charter over 7 → $17,100 at 15 charters
  - Foreign-based unlimited: $24,000 flat. Break-even ≈ 21 charters.
- **Charter logistics:** All charters start in BVI. USVI arrivals use go-fast ferry to Soper's Hole (West End) → pick up on yacht.
- **B1/B2 visa:** US visa category, relevant only for crew transiting US waters (USVI). Not applicable to BVI operations.
- **DPNR registration (USVI):** on to-do list to research.
- **PB website:** intention noted — put all PB info on public-facing website.

### Carry-forward
- Fill in `pb_licenses.md` as registration/permit numbers are obtained
- Build PB public website
- Condo Assistant Stripe billing (Step 3)
- All items on master to-do artifact

---

## Session: July 17, 2026

**Theme:** Condo Assistant — Vercel deploy recovery, UX polish, AI behavior fixes, parking link/coupon code cleanup.

### 🏔️ Condo Assistant

- **Unblocked Vercel deploys (root cause: failing test).** Two consecutive commits had been silently failing to deploy. The build gate is `vitest run && next build` — a single failing assertion in `__tests__/api/chat.test.ts` (line 146) was asserting the old model name (`claude-sonnet-4-6`) after the Haiku switch. Fixed it to `claude-haiku-4-5-20251001`, commit `0e2f03a`. All subsequent deploys succeeded.
- **Added search filter to Properties dashboard (`app/admin/page.tsx`).** Real-time client-side filter by unit name; match count shown in subtitle ("3 of 18 properties"); empty-state card with "Clear search" button. Lucide `Search` icon in the input. Commit `a047eba`.
- **Fixed AI stripping `#` from door/access codes (`lib/rag.ts`).** Codes like `#123456` were stored correctly but the model was dropping the `#` in responses. Fix: wrapped door code and common area code in backticks in the UNIT FACTS block so Markdown preserves them, and strengthened the instruction text: "reproduce every character including any # symbols, letters, or digits." Commit `1099d7a`.
- **Parking links + coupon codes cleanup (Founders Pointe & Fraser Crossing).** Unit 4463 had wrong coupon `193755` → corrected to `332344` and added clickable parking link. Founders Pointe building-level Q&As already had correct markdown links. Fraser Crossing building-level Q&As had a copy-paste error — "How do I enter the garage?" had Founders Pointe's URL; corrected to `https://parkingbase.com/c/frasercrossing/search?` with code `223250`. All Fraser Crossing unit-level codes were untouched and correct. User briefly bulk-set all FP units to `332344` then immediately reverted — each unit restored to its original individual code (4463→`193755`, 4364→`161532`, 4367→`220730`, 4645→`543185`, 4659→`856509`).
- **Guest Access Links page header** (commit `8fdf849` from prior session) — was blocked by Vercel failures; now live. Shows property name next to heading.

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending.
- Perpetual Blue: confirm ABM, open Wise Business, engage tax attorney, fill charter calendar.
- Email admin@mountide.com with contrast therapy benefits.
- Order Kodiak Wholesale guest mugs; broker gift to Jonna Duvernoy.

---

## Session: July 14, 2026

**Theme:** Live app bug fix + deploy, charter pricing, fleet additions, and a big data-enrichment task.

### 🏔️ Condo Assistant
- **Fixed a production bug + deployed it.** Guest (Jasper) was refusing to give the front-door code even though it was on the guest link. Traced it: the code *was* reaching the AI (link.door_code → chat route → RAG UNIT FACTS), but the model was following a stale Schlage-era Q&A ("check your booking confirmation") over the injected fact. Strengthened the system prompt in `lib/rag.ts` (two reinforcing rules) so any door/access/common-area code in UNIT FACTS is given directly — **fixes every unit**. Committed `a103963`, pushed to master → Vercel auto-deploy. Also wired the Condo-Assistant repo with token auth so future deploys are one-command.
- Added **COGS Breakdown** + **$ at Scale** tabs to the SaaS model; ran a **token-cost analysis** (Haiku 4.5: chat ~$0.45/unit, auto-email ~$0.68/unit → $3/unit COGS holds); verified Resend estimate (~$0.15/unit is conservative). At 5,000 units: ~$15K/mo COGS.
- **Shipped a guest-chat feature + deployed it (commits `6148c12`, `ca994f7`).** Added two starter chips shown on chat open (EN/ES/FR), ordered **front-door code first, address second**: "What is the code for the front door?" and "What is the address of the property?". Door code already handled by the earlier UNIT FACTS fix. For the address: wired `properties.address` into UNIT FACTS (`lib/rag.ts` + `app/api/chat/route.ts`) and inject a **Google Maps directions deep link** so the AI answers with the full address + a tappable **Get directions** link (opens turn-by-turn nav) plus any KB directions/parking. Repointed the sticky WiFi/checkout quick-buttons to their new indices (2, 3). Typecheck clean; **204/204 vitest tests pass** — note the build gate is now `vitest run && next build`, so a failing test blocks deploys. **Data dependency:** each property's *Address / Unit Number* field must hold a full street address for the map link to pinpoint.
- **Added a Door Code quick-button (commit `b5234af`).** Third sticky quick-action in the input bar (🔑 KeyRound icon) alongside WiFi + Check out, sending the front-door-code question; row now wraps for narrow phones. Label in EN/ES/FR. Deployed, 204/204 tests pass.

### ⛵ Perpetual Blue — charter pricing
- Reviewed the live website; researched BVI market. Direct comp **ODYSSEA** (same Sanya 57 hull) charters **$29.5K low / $35K high**. Site's "$25K from" is underpriced given the $400K refit + 10-guest capacity.
- **Recommended rate card:** ~$30–32K low / **$38–42K high season** / $46–52K holidays. Built a per-guest table ($30K at 10 guests, −$500/guest, floor $26K at 2).
- Flagged config discrepancy to reconcile: site says 5 cabins/10 guests; memory said 6 cabins/6 guests.

### ⛵ Perpetual Blue — business systems + charter calendar (later same day)
- **Locked the financial/tech stack** (see `project_perpetual_blue.md` → Business Systems): **Mercury Free** banking + **Mercury IO** card ($0 fee, 1.5% cash back, free cards for owner + Captain Sean + Chef Elise) + **QuickBooks Simple Start (~$420/yr) from day one.** Skipping Mercury Plus (recurring/ACH-debit invoicing doesn't fit charter cash flow). Runner-up card: Chase Ink Business Cash.
- Talked through **CRM strategy**: CRM = operational system of record (charters→guests→brokers→payments); QuickBooks = statutory/tax books. User sets up Mercury first, then we build the CRM. QB kept as the "escape hatch" that's optional-but-recommended.
- **Charter calendar artifact** — added **half-day diagonal turnover tiles**: pickup day shades afternoon only (morning open), departure day shades morning only (afternoon open) — reflects PM board / ~10am disembark. Added Turnover ½-day legend + hover tooltips. Changed header **USVI → BVI**. Live: https://claude.ai/code/artifact/95f010e0-f26e-4f86-a9f7-873bfdde8fb0
- **Domain:** user securing **www.perpetualbluebvi.com**. Named crew: Captain **Sean Powell** + Chef **Elise**.
- **First expense logged: $13.19** ($12.99 + tax) — 1-yr domain `perpetualbluebvi.com` from GoDaddy (2026-07-14), declined the $13 privacy + $71 MS365 upsells (advised Zoho Mail Free + free WHOIS privacy, ~$84/yr saved). Started an **Expenses & Receipts** table in `project_perpetual_blue.md`. Plan: once the business email is set up, user grants Orion access to scan receipts → categorize → push into CRM + QuickBooks.
- **Set up Zoho Mail Free end-to-end — fully authenticated & verified.** Two inboxes: `charters@` (Nikunj) + `captainsean@` (Sean Powell). Walked GoDaddy DNS live with the user: MX (mx/mx2/mx3.zoho.com), SPF (GoDaddy `_spfm` merge that resolves to `include:zoho.com include:zohomail.com` — left as-is), DKIM (selector `zmail`), DMARC `p=none` rua→charters@. **Verified every record live via `Resolve-DnsName` against 8.8.8.8 + 1.1.1.1** (caught that GoDaddy's `_spfm` SPF *does* authorize Zoho, so didn't "fix" a working record). Domain ownership verified. **Email operational at $0/yr** (dodged ~$84/yr GoDaddy upsells). Carry-forward: Sean sign-in, 2FA, mobile apps, email signatures, bump DMARC → `p=quarantine` after monitoring.

### 🚗 Rent Cars
- Added **Mazda CX-90, Genesis GV70, Genesis GV80** to the top of the fleet model; adopted user's revised inputs; dropped Supra; renamed Tacoma → **Ram Rebel**; split **Ineos Grenadier + Mercedes R-Class** into a "MAYBES" section (excluded from totals). Confirmed fleet now 15 cars, $940.6K capital, 219% 5-yr ROI. Backed up.

### 🗺️ Other
- Built **Location_Addresses.xlsx** — researched street addresses for 106 Colorado business locations (transaction-style list; store numbers authoritative, city labels often wrong). Color-coded by confidence.
- Added **"second-computer setup checklist"** to the to-do (git PAT, auto-approve, memory sync).
- Answered: yes, can run the same Claude account on 2 machines / 2 projects simultaneously (shared usage limits; watch memory-repo git conflicts).

---

## Session: July 11, 2026

**Theme:** Financial modeling across three businesses + fixed the GitHub backup pipeline and permissions.

### 🔧 Infrastructure & setup
- **Solved the GitHub push (finally).** Memory repo had been stuck local-only for weeks (broken Git Credential Manager). Fixed via a **Personal Access Token** embedded in the remote URL — automated pushes now work from any session. All memory synced to `github.com/nick73patel-hash/orion-memory`.
- **Enabled Smart Auto-approve** (`permissions.defaultMode: "auto"` in `~/.claude/settings.json`) — common safe actions (git, node, edits, backups) run without "Allow once"; destructive actions still prompt.
- Added personal to-do: **set up a passkey** (optional GitHub hardening).

### 💸 Tax
- **IRS Form 843 / Kwong v. United States** — walked through the COVID-era penalty refund. Key catch: Kwong only covers deadlines in the 1/20/2020–7/10/2023 window → **2022 penalty qualifies, 2023 does NOT** (use Rev. Proc. 84-35 for 2023). Drafted the packet (`Projects/Form_843_Kwong_2022.md`); mailed Certified Mail to Ogden, UT before the 7/10 deadline.

### 🚗 Rent Cars
- Built **`Rent_Cars_Fleet_Model.xlsx`** — 16 vehicles, live formulas. Added per user: vehicle kit (winter/summer tires incl. sports cars, roof box, 12V fridge, camping pack), **tire-refresh reserve** (hits during + post loan), **camping upsell revenue**, residual value + 5-yr mileage. Fleet: ~$1.03M capital, ~$338K net/yr during loan, 231% 5-yr ROI.

### 🪟 Windows Import
- **Revisited Dubai** → added as exploratory **Line 3** (thermally-broken architectural aluminum). Verdict: luxury-tier only — ~65% tariff wall, no NFRC-certified US-exporting fabricator. Canada (fiberglass, duty-free) + Poland (alu-clad wood, ~15% via HTS 4418) remain the confirmed lines.
- Built **business plan** (`.docx`), **supplier outreach tracker** (`.xlsx`), and **landed-cost / margin financial model** (`.xlsx`): ~$910K rev, 24% gross margin, ~$58K net at lean Phase-1/2 volumes.
- Confirmed **Nour** as a partner (with Joaquin Lopez).

### 🏔️ Condo Assistant — big push
- **Partner structure locked:** Alex (partner) + **Rob (10%, security/dev, oversees Orion's work)**; removed Eliot.
- Recapped SaaS status: multi-tenancy ✅ + signup wizard ✅ done; **Stripe billing (Step 3) is next**.
- Built the full SaaS package **to show Alex**:
  - **`Condo_Assistant_SaaS_Model.xlsx`** — 7 tabs (Pricing, Revenue Model, Unit Economics, Rollout Ramp, COGS Breakdown, $ at Scale, Assumptions).
  - **`Condo_Assistant_Business_Plan.docx`** — with a visual dashboard first page.
  - **`Condo_Assistant_Snapshot.html`** — one-page browser artifact (alpine-concierge design). Live: https://claude.ai/code/artifact/362f8824-e7cd-4d3b-aefa-200e0ea0caff
- **Pricing evolved** to per-unit ($15 CA / $15 Email / $25 bundle) with a **$500/mo cap** (up to 100 units) that smooths the volume cliff. Client distribution set by user (4,000 @ 1–2 units … 50 @ 50–100). **Result: ~$4.42M ARR, 78% gross margin, 12.7x LTV:CAC.**
- **Strategy:** under-5-unit owners = profit center; large PM companies priced low as a distribution channel.
- **Token cost analysis** (Haiku 4.5): guest chat ~$0.45/unit/mo, auto-email ~$0.68/unit/mo → COGS breakdown sums to $3/unit; verified Resend estimate ($0.15/unit) holds. Added **COGS Breakdown** + **$ at Scale** tabs (revenue/COGS/gross profit at 1K/5K/10K/22K units).
- (User already emailed Alex mid-session; confirmed the economics work.)

### Carry-forward
- **Condo Assistant:** build Stripe billing (Step 3) with review checkpoints for Rob; then key rotation + 1Password before taking cards.
- **Windows:** supplier outreach (Accurate Dorwin, Sokółka, Debesto); binding CBP ruling on HTS 4418.
- **Perpetual Blue:** Kodiak guest-mug sample; broker gift to Jonna Duvernoy (AMWAX Prime).
- Rebaseline Condo Assistant hosting COGS against a real Supabase bill past a few hundred units.

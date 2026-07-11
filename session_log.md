# Orion Session Log

Running log of work sessions. Newest first. Project-specific detail lives in each `project_*.md`; this is the chronological overview.

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

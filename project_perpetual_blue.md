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
- **Accounting: Digits** — AI-powered ledger. CRM will integrate with Digits as the general ledger (not QuickBooks). Receipts will be uploaded via CRM → categorized → pushed to Digits.
- **CRM: to be built next.** Operational brain — charters → guests → brokers → commissions → deposits/payments → expenses + receipt photos — feeding clean categorized data into Digits. Design principle: **CRM = system of record for operations; Digits = statutory/tax books.**

> ⚠️ Earlier (July 6) note: Mercury suits the FL LLC; **Wise Business** was recommended for a BVI BC. If the entity re-flags to a BVI BC, US cards (Mercury IO / Chase) require **keeping the US LLC** — reconcile card entity with the final corporate structure.

---

## Perpetual Blue CRM — Live (status + to-do)

**Live at:** crm.perpetualbluebvi.com · **Repo:** github.com/nick73patel-hash/perpetual-blue-crm (private) · **Stack:** Next.js 16 + Supabase + Vercel · 20 modules
**Status (as of July 29, 2026):** Deployed and working. Nick's admin login is live. Three launch bugs fixed (RLS recursion, self-fetch 500s, nav 404s). See session log for detail.

> ⚠️ **Known gotcha (hit Aug 24, 2026):** Supabase **free tier auto-pauses the DB after ~7 days of inactivity.** When paused, the Vercel-hosted login page still loads fine but ALL auth calls (login + password reset) fail with **"Failed to fetch"** / `net::ERR_BLOCKED_BY_CLIENT`. **Fix:** Supabase dashboard → open `perpetual-blue-crm` project → **Resume**. This WILL recur whenever the CRM sits unused a week. Nick's admin email = **charters@perpetualbluebvi.com**. Permanent fixes: **Supabase Pro ($25/mo, no auto-pause + daily backups)** once it carries live data, or a scheduled keep-alive ping (free but hacky).

**Seeded data:** Grenada & Grenadines "As-Sailed v2" itinerary seeded into the CRM (Aug 24 2026) — table `itineraries` id `11111111-1111-1111-1111-111111111111`, 9 stops in `itinerary_stops`. Sits alongside the built-in "BVI Standard 7-Night" template (id `00000000-...-000000000001`). Schema: `supabase/migration_006_itineraries.sql`.

> 💡 **Supabase SQL-editor paste gotcha:** pasting SQL from chat into the Supabase web SQL Editor **flattens `''` escaped apostrophes and mangles non-ASCII (em-dashes)**, breaking string literals → "unterminated quoted string", and the leftover text `... INTO SVG ...` gets read as `SELECT INTO` a phantom table → `relation "SVG" does not exist` (Supabase then auto-appends `ALTER TABLE "SVG" ENABLE RLS`). **Fix:** write seed SQL as **pure ASCII, zero apostrophes, and avoid standalone `INTO` + capitalized words inside string text.** Clear the editor fully before re-pasting.

**P&L modeling — clearing-house basis CONFIRMED 2026-08-26:** the "Management Expense - Clearing House" line is **2% of GROSS Charter Income + $200/month**. Nick's confirmation: *"it's 2% of charter gross."* On the **HeySea SV60** sheet this is **row 57**, formula `=B27*0.02+200*12` (B27 = gross Charter Income, i.e. **before** the 15% broker fee) → **$11,800 / $16,500 / $21,200** at 10/15/20 charters. Already built on gross — no change was needed.

**Applied to the Perpetual Blue P&L sheet too (2026-08-26, Nick's call).** Row 36 flipped from flat inputs to `=B7*0.02+200*12` (black font per that sheet's *blue = input / black = calculated* legend; note in E36). Row 7 is gross Charter Income, so the basis matches the HeySea sheet. Effect at 10/15/20 charters:

| Line | Was | Now |
|---|---|---|
| Clearing House (row 36) | $5,000 / $7,500 / $10,000 | **$7,800 / $10,500 / $13,200** |
| Total OpEx (row 54) | $286,650 / $338,100 / $397,200 | **$289,450 / $341,100 / $400,400** |
| Net Income (row 58) | ($56,400) / $7,275 / $63,300 | **($59,200) / $4,275 / $60,100** |

> ⚠️ **The 15-charter case is now razor-thin — $4,275 net, down from $7,275.** Breakeven is still between 10 and 15 charters but has moved materially closer to 15. Worth remembering before treating 15 charters as a "safe" plan.

**Backup before the edit:** `C:\Users\ducat\Projects\Perpetual_Blue_PnL_backup_2026-08-26.xlsx` (pre-change state, in case the 2% needs reverting).

**CRM To-Do:**
- [ ] **📌 THIS AFTERNOON (2026-08-26) — commit the `db-backup.yml` prune fix.** The Aug 24 audit agent corrected the backup retention logic (the delete step's file scoping); the fixed file is sitting **uncommitted** in `C:\Users\ducat\Projects\perpetual-blue-crm`. The local PAT lacks `workflow` scope, so it can't be pushed with git — it has to go up through the **GitHub web UI**, same as the original workflow file. Already audited safe: it only ever deletes `backups/perpetual-blue-*.sql.gz.gpg` files past the cutoff (never README/.gitkeep), with `nullglob` guarding the empty case.
- [ ] **▶ RESUME HERE — finish activating the nightly DB backup (Option B, built 2026-08-02).** Free automated GitHub Action (`.github/workflows/db-backup.yml`) runs `pg_dump` nightly → **GPG-encrypts** (AES-256) → commits `backups/perpetual-blue-YYYY-MM-DD.sql.gz.gpg` (ciphertext) to the private repo (30-day retention). **DONE so far:** encrypted workflow file added via GitHub web UI (couldn't push it via git — local PAT lacks `workflow` scope); docs pushed (`BACKUP_SETUP.md`, `backups/README.md`, commit 3551568). **REMAINING (~5 min, Nick does — paused 2026-08-02, tired):** (1) add GitHub repo secret `SUPABASE_DB_URL` = Supabase **direct/session** connection URI — get it via the green **"Connect"** button at the top of the Supabase project dashboard (NOT gear→Database; UI moved), swap in real DB password; (2) add secret `BACKUP_ENCRYPTION_KEY` = a strong passphrase **saved in his password manager FIRST** (GitHub secrets are write-only; losing it = backups unrecoverable); (3) Actions tab → "Nightly DB Backup" → Run workflow → confirm a `.sql.gz.gpg` file appears. Secrets page direct link: `github.com/nick73patel-hash/perpetual-blue-crm/settings/secrets/actions/new`. Restore = fresh Supabase, `gpg --batch --decrypt --passphrase "<KEY>" <dump>.sql.gz.gpg | gunzip | psql "<new-db-url>"`, redeploy app from git. **Nick entered both secrets himself — Orion never handles the DB password or encryption key.**
- [ ] **(Low priority · ~couple months out) Supabase Pro ($25/mo) — Option A.** Flips on daily automatic backups (7-day retention) + optional Point-in-Time Recovery, zero setup. Belt-and-suspenders on top of the free nightly dump. Revisit once the CRM is carrying more live/production data.
- [ ] **Build "Orion assistant" write-endpoint (co-work automation)** — token-protected `POST /api/assistant`, **insert-only** whitelist (`add_expense`, `add_recipe`, `ping`) + a local `scripts/orion.mjs` CLI so Orion can add records for Nick. **Design decision:** the USER generates & holds the secret token (adds it to Vercel env + gitignored `.env.local`); Orion writes code only and never generates/handles the credential; CLI reads the token from `.env.local` at call time. Guardrail: confirm-before-write on anything financial. **Review with Rob first** — grants an AI scoped write access to prod. *(Auto-mode safety classifier blocked auto-building this 2026-08-02 pending a human decision — intentional pause; resume when Rob's available.)*
- [ ] **Create Sean & Elise logins** — add Supabase Auth users (`sean@perpetualbluebvi.com` = captain, `elise@perpetualbluebvi.com` = crew), then link each with `UPDATE crm_users SET supabase_uid = '<uuid>' WHERE email = '...'`
- [ ] **Build Cards & Bank page** — bank accounts + credit cards are already seeded (migration_002_seed), just needs a UI page. Was removed from the sidebar nav on July 29 (pointed to a 404).
- [ ] **Build Balance Sheet page** — was scaffolded in nav but never built; removed July 29.
- [ ] **Full click-through test** of all 20 modules to confirm seeded data renders and nothing else throws.
- [ ] ⚠️ **Rotate the GitHub token** — a PAT (`ghp_...`) is embedded in the git remote URLs for both `perpetual-blue-crm` and `orion-memory`. Not pushed to the repos (local `.git/config` only), but rotate it and switch to a credential helper. Flag to Rob (security).

### Provisioning Pipeline — phased roadmap (guest prefs → AI menu → shopping list)

The big vision: turn the preference sheet into a full pre-charter provisioning pipeline so Elise (chef) walks off the boat with a week's menu + shopping list.

- **Phase 1 (BUILDING now):** Preference Sheet. `guest_preferences` table scoped per-charter, **multiple sheets per charter** (one per guest). Section on the charter detail page. Allergies highlighted red. Print view. Manual entry.
- **Phase 2:** Email cascade + collection. Send a preference-sheet invite to the **broker → primary guest → all other guests**. Each guest fills a **public (no-login) tokenized web form**; answers land back on the charter. "X of Y sheets received" tracker. Needs an **email service** (recommend Resend; verify perpetualbluebvi.com — Zoho SPF/DKIM already set). Rob to review public-form token security.
- **Phase 3:** New **Galley / Menus module** — Elise's recipe library, built over time. Killer feature: **snap a photo/screenshot of a recipe → AI extracts structured recipe** (title, ingredients, qty, servings, steps). Manual entry too. Needs Claude API + Supabase Storage (already used).
- **Phase 4:** **Consolidate** button on a charter → AI merges all guest sheets (combined allergies/hard-no's/likes) → generates a **full week menu** from Elise's recipe library respecting all constraints → generates a **shopping list** scaled to pax. Needs Claude API.
  - **Presentation requirement (user, 2026-07-29):** the generated week menu must render as a **polished visual weekly menu that Elise can easily edit inline** — not a plain text dump. She can override/edit anything before it's final.

External deps to set up before Phases 2–4: **Anthropic (Claude API) billing + key**, **Resend (or other email) account**. AI-feature work should run on **Opus 5** (see [[pref-model-opus5]]).

---

## Charter Calendar (visual artifact)
- **Live:** https://claude.ai/code/artifact/95f010e0-f26e-4f86-a9f7-873bfdde8fb0
- **Source (permanent):** `C:\Users\ducat\Projects\perpetual-blue\pb-calendar.html` · backed up to GitHub at `project_files/pb-calendar.html`. Redeploy: edit the file → Artifact tool with the same URL.
- 2026/2027 season, 7 charters (4 booked, 3 holds, **43 nights**). Nautical theme, theme-aware, monthly grids Dec 2026–Jun 2027. Header labeled "Perpetual Blue · BVI".
- **Half-day turnover tiles:** pickup day = afternoon-only diagonal (morning open, guest boards PM); departure day = morning-only diagonal (guest off ~10am, afternoon open for turnaround). Full nights solid. Legend has a "Turnover ½-day" swatch.

---

## Expenses & Receipts (feeds CRM + QuickBooks)

Running startup-expense log. **Plan:** once the business email (on `perpetualbluebvi.com`) is set up, user will grant Orion access to **scan receipts → categorize → push into the CRM and QuickBooks**. Until then, expenses are tracked here manually.

### Mercury Business Account
Transactions funded directly through the Mercury account:

| Date | Item | Vendor | Amount | Category | Receipt |
|---|---|---|---|---|---|
| 2026-07-24 | Initial deposit | Nick (owner) | **$100.00** | Owner Contribution | — |

**Mercury balance: $100.00**

---

### Personal Funds — Boat Expenses (not yet in Mercury)
Paid personally by owners — to be reimbursed or recorded as owner contributions:

| Date | Item | Vendor | Amount | Category | Receipt |
|---|---|---|---|---|---|
| 2026-07-14 | Domain: **perpetualbluebvi.com** — 1-yr registration | GoDaddy | **$13.19** | Startup · Web & Software | pending |
| 2026-07-28 | Captain Sean Powell — flight DOM → EIS (Tortola, BVI) | TBD | **$242.97** | Crew Travel | pending |
| 2026-07-28 | Elise McNabb — flight SJU → EIS (Tortola, BVI) | TBD | **$108.97** | Crew Travel | pending |
| 2026-07-24 | Crew pay | — | **$11,654.30** | Crew Wages | pending |
| 2026-07-24 | MGH Insurance payment | MGH | **$10,782.00** | Insurance | pending |
| 2026-07-24 | Nick — flight to BVI (deliver boat to Grenada) | TBD | **TBD** | Owner Travel | pending |
| 2026-07-28 | Water weights | TBD | **$64.90** | Boat Equipment | pending |
| 2026-07-28 | Sand weights | TBD | **$33.99** | Boat Equipment | pending |
| 2026-07-28 | Autopilot parts | TBD | **$2,193.49** | Maintenance & Repairs | pending |
| 2025 | Centennial Accounting — 2025 tax filings | Centennial Accounting | **$1,000.00** | Professional Fees | pending |
| 2026-08-02 | Carbon fiber roll | TBD | **$15.00** | Maintenance & Repairs | pending |

**Personal expenses total: $26,108.81** (+ Nick's flight TBD)

> **Receipts:** Physical/photo receipts exist for all personal expenses above. Will be uploaded into the CRM once it's built — receipt photo → expense record linkage is a required CRM feature.

> Email: **Zoho Mail Free** (2 inboxes, set up 2026-07-14):
> - `charters@perpetualbluebvi.com` — **Nikunj** (owner; bookings/brokers)
> - `captainsean@perpetualbluebvi.com` — **Sean Powell** (captain)
>
> Chosen over GoDaddy's $71/yr MS365 upsell; free WHOIS privacy instead of the $13/yr add-on → ~$84/yr saved. Free plan = webmail + mobile app only (no IMAP/desktop-client unless upgraded to Mail Lite ~$1/user/mo).
>
> **Fully configured & verified 2026-07-14.** DNS in GoDaddy: MX (mx/mx2/mx3.zoho.com), SPF (GoDaddy `_spfm` merge that resolves to `include:zoho.com include:zohomail.com`), DKIM (selector `zmail`, live + verified), DMARC currently `p=none` with rua→charters@ (**bump to `p=quarantine` after monitoring**). Domain ownership verified by charters@. TODO: get Sean signed in (was "never signed in"), enable 2FA, mobile apps, draft email signatures.

---

## Tax Structure — Key Findings (July 24, 2026)

### Ownership Structure
**Recommended: 3 US citizens → BVI BC directly (no FL LLC intermediary)**
- FL LLC middle layer produces identical federal tax results at $3–10K more/year in cost
- Both structures result in the BVI BC being classified as a CFC — the FL LLC changes nothing

### Annual Compliance Cost
**~$1,500–3,000/year total** — mostly BVI fees, not accounting:
- BVI government fee: $550–1,350/year
- BVI registered agent: ~$1,000–1,500/year (required by BVI law)
- IRS forms (5471, FBAR, 8938, 8992): free to file yourself

### Key Decision at Formation: CFC vs. Partnership
File **Form 8832** at formation to elect partnership treatment OR do nothing (defaults to CFC/foreign corporation):
- **CFC (default):** No self-employment tax. Losses stay trapped in entity (can't offset other income). Small profits taxed at ~12.6% with §962 election. Forms: 5471 + 8992.
- **Partnership election:** Losses flow through to owners (but likely passive — can only offset passive income). SE tax risk if members are active. Forms: 8865 (cheaper/simpler than 5471).
- **For a mostly break-even boat:** Either works. Partnership may be slightly simpler/cheaper to file.

### Why Annual Tax Bill is Low for This Boat
- At-or-below breakeven: no taxable income → no tax owed on inclusions
- $60K retained for maintenance: ~$20K/person share → ~$2,500/person at 12.6% rate (trivial)
- BVI 0% corporate tax = no foreign tax credits, but also no BVI tax bill

### IRS Forms (all US-filed, nothing goes to BVI)
- **Form 5471** — CFC annual reporting. Can DIY but complex. $10K penalty for missing it.
- **Form 8992** — NCTI/GILTI calculation. Goes with 5471.
- **FBAR (FinCEN 114)** — Free, online, 15 minutes. bsaefiling.fincen.treas.gov
- **Form 8938** — FATCA. Bundled with 1040.
- **Form 926** — One-time at formation when yacht transfers to BVI BC.
- **Form 8832** — One-time at formation if electing partnership treatment.

### BVI Economic Substance Act
Charter yachts are **explicitly excluded** from BVI Economic Substance requirements (BVI ITA 2023 guidance). No BVI-resident directors, premises, or staff required. File annual declaration confirming no relevant activity.

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

## Yacht Management Company (Sub-Project)

**Partners:** Sean, Mason  
**Model:** Manage fleet of crewed charter yachts for other owners — full-service: maintenance, crew, charters, compliance  
**Target fleet:** 15 yachts  
**Positioning:** Below-market fees, full financial transparency (all invoices uploaded to CRM so owners see every cost)

### Pricing Structure (decided July 2026)
- **Monthly retainer:** $1,000/yacht/month (flat management fee)
- **Performance fee:** 10–15% of net profit per charter (after expenses)
- **Positioning:** ~10% below market rate — market is 15–25% of gross; our model uses net = more owner-friendly
- **Key differentiator:** Full receipt transparency via CRM — what makes "% of net" viable (owners can verify every expense)

### Baseline Overhead (before first yacht)

**One-time setup: $4,000–7,000**
- Entity formation (USVI LLC or BVI BC): $500–1,500
- Management agreement (maritime attorney): $2,000–4,000
- Website: $500–1,500

**Monthly fixed burn (~$1,400–2,500/month):**
| Item | Monthly Est. |
|---|---|
| Professional liability / E&O insurance | $400–700 |
| General liability | $75–150 |
| CRM + receipt management software | $100–300 |
| Bookkeeping / QBO | $50–100 |
| Business banking | $50–100 |
| Communication | $100–150 |
| Website hosting | $30–50 |
| **Total** | **$805–1,550/mo** |

**Annual baseline: ~$25,000–35,000/year** (dominated by E&O insurance: $5,000–10,000/yr)

### Regulatory Requirements
- **USVI:** Business license via DLCA (~$250/yr)
- **BVI:** FSC business registration if BVI entity (~$500–900/yr registered agent)
- **Industry:** BVI Charter Yacht Society membership (~$500–1,000/yr)
- No separate maritime license for *management company* — that's on captain/vessel

### Unit Economics (per yacht, 15-charter season)
- Management fee: $12,000/year
- Performance fees @ 12% of ~$17K net/charter × 15 charters: ~$30,600/year
- **Gross revenue per yacht: ~$42,600/year**
- Variable cost per yacht (travel, inspections): ~$3,000/year
- **Net contribution per yacht: ~$39,600/year**
- Break-even: ~1 fully active yacht covers fixed overhead

### Proforma Files
- `C:\Users\ducat\Projects\Yacht_Mgmt_Proforma.xlsx` — scenarios: 1, 5, 10, 20 yachts (general overhead model)
- `C:\Users\ducat\Projects\HeySea60_QuarterShare_Proforma.xlsx` — fractional ownership deal model (see below)

### Hey Sea 60 — Quarter Share Fractional Program
**Vessel:** HeySea Seaview 60  
**Price:** $1,810,000  
**Structure:** 4 quarter shares × $452,500/owner  
**Charter type:** Full crewed (Captain + Chef/Mate) — NOT bareboat  
**Positioning:** Lower-cost alternative to Leopard 58 (~$1.9M+) or Lagoon 55 (~$1.8–2M) in crewed charter space  
**Target charter rate:** $35,000–55,000/week (crewed 60ft cat)  
**Builder:** HeySea Yachts, Jiangmen China — semi-custom, superyacht heritage  
**Depreciation assumption:** 55–65% residual at year 5 (no track record — conservative vs. Bali/Leopard's 60–70%)  
**Securities law:** Must structure shares carefully — consult securities attorney before selling (Reg D or equivalent)  
**Builder file:** `C:\Users\ducat\Projects\condo-assistant\perpetual-blue\build_heysea_proforma.js`

### Yacht Financing — BVI-LLC + Charter (research Aug 3 2026; general info, not advice)
**Deal profile researched:** 4 US co-owners → **BVI LLC** → ~60ft **HeySea** (~$2.2M, Chinese-built) → likely **BVI-flagged** → **commercial charter** in BVI. Ask was 25% down / ~73% LTV / 20yr.
- **Feasibility (honest):** 73% LTV is a long shot. **Plan for ~40–50% down (50–60% LTV), 10–15yr term.** Four hurdles stack: (1) **charter/commercial use** = biggest LTV killer (specialist charter lenders cap ~50% LTV, FICO 760+, ~10yr term); (2) **newer Chinese builder** = collateral/resale discount → lower advance; (3) **BVI flag** = needs a lender who'll perfect a **BVI ship mortgage** (not USCG); (4) **BVI LLC** = corporate loan to SPV + **personal guarantees from all 4 owners**.
- **Loan ~$1.6M is a "tweener"** — above consumer boat lenders (~$100k cap: Medallion, LightStream) but BELOW superyacht-bank floors (~$3.5M: Excel Credit; European private banks). Sweet spot = **US specialist marine lenders + a broker**.
- **Best contacts to call (lead with brokers, not banks):** **Newcoast Financial** (explicitly does foreign-registered + charter — closest match), **Sterling Acceptance** (Annapolis, large-yacht specialist), **Trident Funding** (big marine broker), **Coastal / coastalboatloan.com** (dedicated charter program), **Enness Global** (int'l arranger across 500+ lenders + European private banks, serves US clients). **Excel Credit** = the published rulebook (accepts BVI flag + charter but max 50% LTV, 760+ FICO, 10yr, **$3.5M min** so likely too small). **Moorings/Essex/LaVictoire** do 25%-down charter loans but only for **managed fleets w/ approved (non-Chinese) builders** → HeySea doesn't fit.
- **Path:** lead with a broker; give full picture (BVI LLC + flag + charter + HeySea) day 1; they underwrite the **4 guarantors' personal credit + a commercial charter business plan** (several programs IGNORE projected charter income); budget 40–50% down; get **survey/valuation early** (blunt the Chinese-builder discount); **maritime attorney** for BVI mortgage; **CPA** for CFC/interest-deductibility. If owners have investable assets, a **private-bank / lombard** loan may be the only route to higher advance + 15–20yr term.

### Office / Operations
- Work-from-home initially
- Eventually: small office + storage in **Red Hook, St. Thomas USVI** (~$2,000–3,500/month when needed)

---

## Open Questions / Action Items

- [ ] **Change the credit card info on Sean's computer** (update the saved payment method)
- [x] Hire regulatory agent for BVI registration — **ABM Group hired (July 2026)**
- [ ] Confirm ABM is handling CRVL + Charter Authorization Letter (not just company + vessel registration)
- [ ] Open **Wise Business** account for BVI BC banking (primary); add Butterfield Bank BVI as secondary
- [ ] **Tax:** Decide CFC vs. partnership treatment for BVI BC at formation — file Form 8832 (check-the-box election) if electing partnership. Do this before entity begins operating.
- [ ] **Tax:** File Form 926 when yacht transfers into BVI BC (required if yacht value >$100K — it is)
- [ ] **Tax:** Year 1 — get a one-time consult with international tax CPA to walk through Form 5471 setup; then DIY ongoing. Form 5471 can be filed yourself but is complex.
- [ ] **Tax:** File FBAR free at bsaefiling.fincen.treas.gov annually if BVI BC has >$10K in foreign accounts
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

## Shareable Artifacts (permanent links)

| Artifact | URL |
|---|---|
| **Grenada Itinerary** — Aug 12–21 2026, 10-day sailing itinerary | https://claude.ai/code/artifact/23c607f8-29df-4b6a-a4fe-0b5c081e3b82 |
| **HeySea Seaview 60 Research Brief** — BVI charter evaluation, build quality, risks, due diligence | https://claude.ai/code/artifact/f0c44b26-02a1-46bc-be5e-2bf7ea6e8427 |

> Note: Both source HTML files are also in the scratchpad. To update, re-publish with the `url` parameter to keep the same link.

---

## Research Notes
- BVI charter research (HeySea vs. FP vs. Bali): conducted July 2026 — 18 sources, full report published as artifact above
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

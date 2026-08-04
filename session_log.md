# Orion Session Log

Running log of work sessions. Newest first. Project-specific detail lives in each `project_*.md`; this is the chronological overview.

---

## Session: August 3, 2026 — Provisioning pipeline overhaul + AI-key unblock + yacht/Navionics research

**Theme:** Deep live-use refinement of the Phase-4 provisioning pipeline (menu → shopping list → chef workflow), plus a critical bug unblock and two research asks. All build work delegated to background subagents per [pref_delegate_subagents.md](pref_delegate_subagents.md); Orion verified + committed/pushed each. All `perpetual-blue-crm` pushes succeeded.

### 🔑 THE critical fix — AI features were all silently broken (`1678f72`)
- Consolidate returned *"missing ANTHROPIC_API_KEY."* Root cause: the Vercel env var was named **`Anthropic_API_Key`** (mixed case) but env names are **case-sensitive** and the code reads `ANTHROPIC_API_KEY`. Vercel **won't let you rename** a var, and the value is Sensitive/unreadable, so instead of re-entering the key, Orion fixed it **code-side**: new `lib/anthropic.ts` with `getAnthropicApiKey()` (reads `ANTHROPIC_API_KEY ?? Anthropic_API_Key`) + `makeAnthropic()`; wired into all 5 AI routes (consolidate, menu, provision, recipes/extract, expenses/extract). This unblocked **every** AI feature at once (they share the key). Tolerant lookup, so a future proper-named var still works.

### 🍽️ Menu pipeline overhaul (`fa6119e`) — one big coherent build
- **Date-aware days:** new `lib/provisioning/days.ts` (`buildCharterDays`, `allowedMealsForDay`, `normalizeMenu`). Menu lays out against the real charter calendar (start_date→end_date inclusive); **check-in day = no breakfast/brunch**, **checkout day = no dinner**. `normalizeMenu` defensively stamps dates/labels, drops meals on disallowed days, and **trims to the exact meal counts** (4 dinners → exactly 4).
- **Per-meal UI:** each meal now has a **"View recipe"** toggle (Galley recipes, expand/collapse inline) OR a **web source link** (internet suggestions), plus a **"Make new suggestion"** button → new endpoint `app/api/charters/[id]/menu/regenerate-meal` regenerates ONE meal (avoids the rejected dish, leaves the rest).
- **Sourcing = Galley first, then internet:** menu route + regenerate use a **two-step** call (web_search `web_search_20260209` gathers real recipe ideas w/ source URLs, then structured-output assembles the Menu JSON — the two can't combine in one call). Galley meals carry `recipe_id`; web meals carry `source_url`. `MenuItem` gained `source`/`recipe_id`/`source_url`. Galley is ~empty now so most suggestions are web-sourced to start.
- **Note:** the build subagent stalled on the stream watchdog (web_search is slow) but had finished all files; Orion verified tsc-clean + shipped. Menu gen is now slower + adds a small per-search API cost — expected.

### 🛒 Provisioning fixes
- **Headcount fix (`8853172`):** shopping list now scales to `max(charter.pax, sheets) + 2 crew` (Captain + Chef), NOT the returned-sheet count — so 8 guests / 6 sheets provisions for **10**. Menu still reflects the sheets' tastes; groceries feed everyone aboard. Shows "Provisioned for N people (X guests + 2 crew)".
- **Copy Full Brief + checkable list (`3df7733`):** new `lib/provisioning/format.ts` (shared text formatting). **"Copy Full Brief"** button = one email-ready text bundle (preference sheets → consolidated → menu → shopping list). **Checkable mobile shopping list** — checkbox left of each item → strikethrough + dim when found; **persists per-charter in localStorage** (`pb-provision-checked-<charterId>`) so a reload mid-shop survives; "Clear checks" + "X of Y found". UI-only, never touches the DB.

### Smaller CRM tweaks
- **`ANTHROPIC` aside:** also confirmed the receipt scanner + recipe extract now work (same key fix).
- **Copy All Preference Sheets** button on the charter page (`2b0cdb3`).
- **Calendar defaults to 2027** (`3c79da7`); **"Tools"** added to maintenance to-do categories (`be8fe9c`).
- Charters KPI/2027 default + Meals category shipped the prior day.

### 🍸 Bar Inventory — new voice-driven module (`626db9c`, migration `022`)
- New **Bar Inventory** module under **Galley** (`/galley/bar`; sidebar: Galley → Recipes + Bar Inventory) for the **Captain** to manage **liquor / wine / beer** stock. Table `bar_inventory` (name, category, quantity numeric, unit bottle/case/can/keg, notes) — migration `022` (RUN by user, success).
- **Voice capture:** mic button (Web Speech API) → Captain speaks the bottles → editable transcript → **"Add to Inventory"** → server route `/api/bar-inventory/parse` (`claude-sonnet-5`, structured JSON) parses free speech into clean line items (spoken numbers→digits, brand normalization, Liquor/Wine/Beer classification, unit inference) → **preview/confirm** (editable, catches mis-hears) → **Confirm & Add**. Web Speech API is Chromium/Safari-only → graceful fallback to keyboard 🎤 dictation / typing / manual add form.
- **Three ways to manage a count:** voice/manual = **ADD** (accumulate, case-insensitive name+unit match); per-item **−/+ buttons** = one-tap used/bought-one (− clamps at 0; 40px touch targets for iPhone); inline number editor = **SET** exact value (full stock-take). Endpoints: `bar-inventory/route.ts` (GET+POST accumulate), `bar-inventory/[id]/route.ts` (PATCH set/adjust + DELETE). Reason for +/−: Captain needs to reduce stock after usage AND top up after shopping, not just re-count.

### 📊 Perpetual Blue P&L — updated + HeySea SV60 added (`Perpetual_Blue_PnL.xlsx`)
- **File:** `C:\Users\ducat\Projects\Perpetual_Blue_PnL.xlsx` (built via Node + ExcelJS at `C:\Users\ducat\Projects\project-budgets\` — NOTE: no Python on this machine; use Node/ExcelJS for xlsx). Two sheets now.
- **Sheet "P&L" (Perpetual Blue / FP Sanya 57)** — updated with Nick's new values: Charter Income 270k/405k/540k (=$27k/charter × 10/15/20), Broker 15%, Total OpEx 286,650/338,100/397,200 (incl. $43k mortgage + $23k insurance + $10k BVI licenses etc.), Net Income (56,400)/7,275/63,300. Breakeven between 10–15 charters.
- **Sheet "HeySea SV60"** — NEW comparison boat P&L, **2 financing scenarios**. Assumptions (editable/amber): boat **$2.3M delivered BVI**, charter **$47,000/7 days** (a real SV60 is chartering at this — validated), 10/15/20 charters, opex reused from Perpetual Blue EX-mortgage, financing 25% down / 20yr / ~7.5%. **Cash purchase:** debt service $0 → strong net (~$304k @ 15 charters, ~13% on the $2.3M). **Financed (25% down = $575k, loan $1.725M):** annual debt service ~$167k → ~$137k net @ 15 charters, ~24% cash-on-cash on the $575k down — leverage wins on ROC above ~11–12 charters. **TODO:** true up insurance (still carries FP's $23k, not the $2.3M hull) when quotes come.
- **Tax-benefit lines added (per Nick):** under EACH scenario's cash-on-cash, two more lines — (a) **+ $100K/yr depreciation** and (b) **+ depreciation & §179 write-off (Year 1)**. Mechanic: deduction × tax rate = tax savings added back to cash return. New editable assumption cells (rows 14–16): Tax Rate 35%, Annual Depreciation $100K, §179 $1.22M (2024 cap). Depr shield = $35K/yr; §179 benefit = $427K one-time Yr-1. Results — **Cash (on $2.3M):** 15 charters 13.2% / 14.7% / 33.3%. **Financed (on $575K down):** 15 charters 23.9% / 30.0% / 104.2% (the §179 Yr-1 spike is huge on the small down payment). Sheet notes flag it's ILLUSTRATIVE / not tax advice — §179/MACRS eligibility for a charter yacht owned via a BVI entity is uncertain (confirm w/ CPA). Rebuilt via `project-budgets/build-heysea-v2.js` (ExcelJS load → remove+re-add HeySea sheet, P&L sheet untouched).
- Delivered to Nick via chat + local file. Google Drive upload via connector FAILED (base64 corrupts over the tool boundary) — Nick to drag-drop into Drive "Perpetual Blue" folder (auto-converts to Sheets), or retry.

### 💵 Rate Cards — made editable + tuned
- **Editable rate cards (`1fa1e1e`):** added the missing Edit path — pencil button on each card → `/rates/[id]/edit` → `RateForm` prefilled → Save via PATCH. (Form + PATCH route already supported edit mode from prior work; just needed the page + button.) Create/delete/active-toggle unchanged.
- **Defaults to 2027 (`c5397fc`)** — now Charters + Calendar + Rate Cards all open on 2027 ("into the future").
- **APA → Suggested Gratuity (`b716e4f`):** removed the "Suggested APA (30%)" line from the Quote Calculator (APA doesn't apply — BVI/Caribbean charters are ALL-INCLUSIVE). Replaced with **Suggested Gratuity 15%** + note "10–20% customary · most crews ~18% · discretionary, paid to crew on top of the all-inclusive fee." Total Guest Pays now = charter fee (marked all-inclusive); copy-quote updated.

### 🛥️ Research (not saved as files — summarized here)
- **New ~80ft sail cats, delivered Tortola BVI:** **Lagoon EIGHTY 2** (real 81'9" flagship sail cat, replaced Seventy 7) — base €6.95M / on-water €7.9M, all-in delivered ~$8.8–9.8M. **HeySea SV60/SeaView 60** — turns out it's a **sailing** cat (Bill Dixon rig), base €1.399M(~$1.52M)/charter-kitted $1.81M, all-in ~$2.0–2.35M. **HeySea Seaview 80** power cat ~$7–8.7M all-in.
- **~60ft sail cats:** **HeySea SV60** (~$2.0–2.35M all-in) vs **Fountaine Pajot Samana 59** (base €1.838M, all-in ~$2.6–2.9M). FP is the fleet-consistency pick (Nick already runs an **FP Sanya 57**). HeySea ~$550k cheaper.
- **BVI duty angle (big):** since **June 1 2025**, a **BVI-Company-owned, BVI-registered** vessel imports **duty-free** (vs 5% standard) — Perpetual Blue's planned BVI BC + re-flag route could zero the duty; then just CRVL ~$2k/yr home-based. **Zero US tariff** (never enters US customs) — matters for China-built HeySea.
- **Navionics:** region-based subscription; BVI = **Caribbean & South America ~$49.99/yr** (Navionics+). Platinum+ (satellite/3D/aerial, ~$65–100+) worth it for BVI reef-spotting. One sub covers **iPhone + iPad** (2 devices, same Navionics account, Apple-to-Apple). Price ~doubled from $24.99 a couple years back.

- **AI song-maker apps (iPhone):** winner **Suno** ("Suno - AI Songs & Music", ~10 free songs/day, Custom-lyrics mode — paste your own lyrics w/ names/story + `[Verse]`/`[Chorus]` tags, pick genre/vocal, ~2–3 min export). Runner-up **Udio** (better audio, only ~3 free/day). Newcomer **ElevenLabs "Eleven Music"** (Apr 2026, $9.99/mo, best quality + cleanest commercial licensing). Tips: write your own lyrics for name-specific songs, spell tricky names phonetically, regenerate. Free tiers = watermarked/non-commercial. Avoid App Store wrapper/scam clones — install first-party only.
- **BVI company + US-citizen remote work (for the Yacht Management Co sub-project):** A US citizen in the US **CAN** 100%-own a BVI entity (BC or LLC), be sole director/manager, **work for it remotely from the US with NO BVI work permit** (permits are territorial — only physical work IN BVI needs one), pay themselves (no BVI income tax), and visit within the 30-day (→ ~6-month) visitor limit. Ownership ≠ residency/right to work locally. **Would** need permits: owner physically working in BVI, OR any staff physically working in BVI (each needs one); living there needs a residence permit. **BVI LLC** often preferred for US pass-through tax vs BC (corporation). **🚩 Key open item: Economic Substance** — a management/services company may be a "relevant activity" (headquarters / shipping / distribution-&-service-centre); if in-scope it could require BVI-based direction/staff/office (opposite of remote) — must be classified by a BVI economic-substance adviser; possible outs = structure outside a relevant activity or evidence tax-residency elsewhere. Confirm with BVI corporate+immigration attorney (O'Neal Webster/Harneys/Conyers) + ES adviser + US intl-tax CPA (CFC/5471/GILTI/FBAR, already known).

### Notes / still open
- **⏳ DB backup STILL not activated** — the 2 GitHub secrets (`SUPABASE_DB_URL` + `BACKUP_ENCRYPTION_KEY`) + test run remain (▶ RESUME HERE on the CRM to-do). Orion cannot run it (no DB credentials by design). Nick paused it (couldn't find Supabase "Connect" button; tired).
- **Set up real email delivery** (Zoho/Resend SMTP) still open — reset emails don't send (Supabase default mailer); Nick's CRM login email is his **Gmail**.
- Housekeeping someday: recreate the Vercel var with the standard `ANTHROPIC_API_KEY` name (the tolerant helper makes this non-urgent).

---

## Session: August 2, 2026 — Data durability + live-use CRM fine-tuning (backups, maintenance to-do, receipt AI)

**Theme:** First real-use session — Nick is now actively using the CRM (logging receipts, adding to-dos). Focus split between **data durability/backups** and a batch of live-use refinements. Nearly everything delegated to background subagents per [pref_delegate_subagents.md](pref_delegate_subagents.md); Orion committed/pushed each as it landed. All `perpetual-blue-crm` pushes succeeded.

### 💾 Data backup / disaster recovery (the headline)
- Explained the two layers: **code is safe in GitHub**; the **live data lives only in Supabase** and had no backup. On Supabase Free there are no automatic DB backups → real risk is human error (bad delete/migration).
- **Built Option B — free automated nightly DB backup** (`.github/workflows/db-backup.yml`): GitHub Action runs nightly `pg_dump` → **GPG-symmetric AES-256 encrypts** → commits `backups/perpetual-blue-YYYY-MM-DD.sql.gz.gpg` (ciphertext) to the private repo, 30-day retention. **Encryption was added after the first version committed plaintext PII** (safety classifier flagged it — good catch; do NOT commit unencrypted guest data). Fail-safe guards (pipefail + empty-file) so bad dumps never commit.
- **Push gotcha:** the local PAT lacks `workflow` scope, so git can't push `.github/workflows/*`. Docs pushed via git (`3551568`); **Nick added the workflow file via the GitHub web UI** (which bypasses the scope limit) — confirmed in repo (`e23b1b2`).
- **⏳ REMAINING (tomorrow, ~5 min):** add 2 GitHub secrets — `SUPABASE_DB_URL` (Supabase **green "Connect" button** → direct/session URI; the gear→Database path moved) + `BACKUP_ENCRYPTION_KEY` (strong passphrase, save in password manager FIRST) → run the workflow once to test. Full steps in repo `BACKUP_SETUP.md` and on the CRM to-do (marked ▶ RESUME HERE).
- **Option A (Supabase Pro $25/mo, daily auto-backups + PITR)** logged as low-priority (~couple months out).

### 🔧 Maintenance To-Do List — new module (migrations `020` + `021`)
- **`020`** — `maintenance_todos` punch-list table, separate from the maintenance LOG. 4 priority levels (Low/Medium/High/Critical), a "Planned Work" **cost roll-up** of open items, **dashboard alerts** (Critical→critical, High/overdue→warning via new `alerts.ts` section 2b), sidebar child under Maintenance.
- **Complete → Maintenance Log flow, refined per Nick:** completing an item opens an inline **"Actual cost"** input (pre-filled with the estimate) + a **"Log it → Maintenance Log"** button. Logs to `maintenance_log` with the **actual** price (`actual_cost ?? estimated_cost`), marks done, drops to the dimmed Completed section showing "Logged: $X." (`021` adds the `actual_cost` column.)
- Added **"Tools"** and (separately) the module respects the Show-Prep category.

### ⛵ Charters
- **Avg Nightly Rate KPI** (5th tile): shows **gross headline** (revenue÷nights, colored green ≥ goal / amber below) + **net after broker fees** underneath + goal line. Goal set to **$4,000/night** (Nick lowered from 4,250).
- Charters list now **defaults to the 2027 season** (`?season=all` still shows every year).

### 🧾 Expenses — receipt capture + AI auto-fill
- **Live webcam "Take Photo"** added to `ReceiptUpload` (getUserMedia, rear camera on mobile) alongside file upload → saves to `pb-receipts`. (Existing "Attach Receipt" already gave phone camera.)
- **AI receipt scanning** (`app/api/expenses/extract` — cloned from the working recipe extractor, **Claude Sonnet 5** vision + structured JSON): after a receipt photo, auto-fills **vendor / description / amount / date / category** (empty fields only, never auto-submits). Uses existing `ANTHROPIC_API_KEY`. Receipt field-lives-inside-the-expense-form (not the list) — reached via ✏️ edit or Add Expense.
- Added **"Meals"** expense category (form dropdown + list filter + warm-amber badge + taught the AI scanner about it).

### Notes / decisions
- **Login:** Nick forgot the CRM password; reset email never arrived because the account email is his **Gmail** (not charters@). Reset page shows "success" even for a non-matching email (anti-enumeration). He got back in on his computer. **To-do: set up real email delivery (Zoho/Resend SMTP)** so Supabase auth emails actually send.
- **Migrations run by user this session (all success):** 014, 016, 018, 019 (bundled), then 020 + 021.
- Commits: `eaab667`, `1e25ca7`, `3c1c0a0`, `863f07c`, `3551568`, `be8fe9c`, `633e46d`, `52259d8`, `d3e2c93`.
- Nice moment: costed the CRM vs. hiring a dev shop — ~**500–800 human-dev hours / $40K–$140K** equivalent, built in ~3–6 hrs of active Orion time for the cost of a subscription + pennies of API.

---

## Session: July 29, 2026 (continued 3) — Phase 4 provisioning pipeline + auth/reviews/charter fixes

**Theme:** Same rolling session. Shipped the **Phase 4 provisioning pipeline** (the whole consolidate → AI menu → shopping list vision), plus password reset, a Surveys→Reviews rename with a public review form, preference-sheet refinements, and charter form/caching fixes. Many pieces built by parallel background subagents; a couple stalled on API errors and were finished by hand.

### ⛵ Perpetual Blue CRM — commits `36cc5c1` → `05811d2`

- **Forgot/Reset password** (`36cc5c1`) — Supabase Auth reset flow. Login page "Forgot password?" link → `/forgot-password` (sends the email) → `/reset-password` (PKCE code exchange / recovery event → `updateUser`). Both public routes allowlisted in `proxy.ts`. **User must add the reset-password URL to Supabase Auth → URL Configuration → Redirect URLs.**
- **Surveys → Reviews** (`cbef285`) — renamed the module user-facing (route `/surveys` → `/reviews`, labels), and BUILT the missing **public guest review form** at `/review` (star ratings + rebook/recommend + comments) posting to the existing `/api/surveys/submit`; allowlisted `/review` + `/api/surveys/submit` in proxy (precise match so internal `/reviews` stays auth-gated). Internal table `guest_surveys` + `/api/surveys/*` names kept.
- **Preference sheet refinements** (`8e85473`, `55a41fe`, `fa78c04`, `fa836a8`, **migrations 014/016/018**): added drinks/day + meal-portion selects (014), optional guest_email/guest_phone (016), coffee_preference (018); hid Cabin Preference from the UI (column kept for reuse); renamed "Alcohol Preferences" → **"Alcohol Preferences and Mixers"** and reordered drinks (alcohol → drinks/day → non-alcoholic → coffee). Static "Available on board" gear list (snorkels, BOTE paddleboard, inflatable kayak, BOTE Water Island, 2 water scooters) above the activities fields on both forms.
- **Charter form UX** (`a230083`, `05811d2`): **nights auto-calculated** from start/end dates; **season auto-derived** from the start-date year (was causing new charters to miss the calendar when left blank); end-date picker nudges to the start date (`min` + auto-fill).
- **Bug fix — new charters not showing** (`05811d2`): the charters list + calendar served STALE cached snapshots. Added `export const dynamic = 'force-dynamic'` to the `(crm)` layout (covers all list pages) + the charters/calendar pages. Root cause combined with the null-season issue above.

### 🍽️ Phase 4 — Provisioning pipeline (the big finale)
- Backend (`648df43`, **migration_019** `charter_menus` table; shared `lib/provisioning/types.ts` + `meal-counts.ts`): three **Opus 5** endpoints under `app/api/charters/[id]/` — `consolidate` (merges all guest sheets into one master; **allergies unioned as absolute vetoes**), `menu` (builds the week from Elise's Galley recipes first + suggestions, respects allergies/diet, hits meal-count targets), `provision` (categorized shopping list scaled to pax). All use structured JSON output; streamed for large outputs.
- **Meal-count defaults (approved):** whole numbers, nights 4–7. Half board 7=7/4/3, 6=6/3/3, 5=5/3/2, 4=4/2/2. Full board 7=7/7/5, 6=6/6/4, 5=5/5/4, 4=4/4/3. "2 meals/day" folds breakfast+lunch into one brunch. Chef can override any count.
- UI (`b7cdf32`): `ProvisioningManager` on the charter page — Consolidate button → red-flagged master summary → meal-plan picker → **editable weekly menu** (add/edit/remove, Save persists via new `menu/save` endpoint) → shopping list → **Copy** button (menu + list as text). Email export deferred to Phase 2b.
- **Deferred:** live web-search recipe research inside menu-gen (kept structured/allergy-safe for v1).

### Notes / decisions
- **New user preference:** delegate build tasks to BACKGROUND subagents so Orion stays free to converse — see [pref_delegate_subagents.md](pref_delegate_subagents.md).
- **"Orion assistant" write-endpoint** (so Orion can add expenses/recipes directly) — spec'd but DEFERRED pending Rob's review; the auto-mode safety classifier blocked auto-building it (intentional). On the CRM to-do in [project_perpetual_blue.md](project_perpetual_blue.md).
- Expense logged: carbon fiber roll $15 (personal boat-expenses table).
- My Caribbean Charters (MCC) — reputable BVI brokerage, potential broker partner (research done, not saved as a file).
- **Migrations the user must run for full function:** 014, 016, 018 (preference fields), 019 (charter_menus). 013/015/017 already run.

---

## Session: July 29, 2026 (continued 2) — Recipe Galley + AI photo→recipe extraction (Phases 3a & 3b)

**Theme:** Continued the Perpetual Blue CRM provisioning pipeline — added guest contact fields, built the chef's recipe library ("Galley"), and shipped the first live AI feature: snap a recipe photo → Claude reads it → auto-fills the recipe form. Anthropic API wired into the deployed app. All commits pushed to `perpetual-blue-crm`.

### ⛵ Perpetual Blue CRM — commits `fa78c04`, `38d2a0e`, `559dd2c`

- **Preference sheet — optional contact fields** (`fa78c04`, **migration_016**): added `guest_email` + `guest_phone` to the internal + public forms and the view/print contact line. Optional everywhere.
- **Phase 3a — Galley recipe library** (`38d2a0e`, **migration_017**): new `recipes` table (title, category, servings, `ingredients` jsonb `[{quantity,unit,item}]`, `steps` jsonb string[], `tags` text[], `source_image_url`, notes). New **Galley** module under `/galley` (ChefHat sidebar nav): card-grid list, add/edit form with repeatable ingredient rows + steps textarea, detail view, photo upload. **Photos reuse the existing `pb-receipts` Storage bucket** under a `recipes/` path (same as expense receipts — no new bucket).
- **Phase 3b — photo→recipe AI extraction** (`559dd2c`): added `@anthropic-ai/sdk`. Server-side route `app/api/recipes/extract/route.ts` calls **Claude Sonnet 5** (`claude-sonnet-5`) with the recipe photo (base64 image block) + **structured JSON output** (`output_config.format` json_schema) → returns a validated recipe object. An "✨ Extract from photo" panel in the recipe form pre-fills every field; the chef reviews/edits then saves. Refusal → 422; missing key → clear 500. Key read server-side only.

**API key setup (done by user):** created an `ANTHROPIC_API_KEY` in the Anthropic Console (existing account — API billing is separate from a claude.ai subscription; $2.55 credit + auto-reload), added it to **Vercel → Environments → Environment Variables** (Production), and redeployed. Feature tested — **working**. Model runtime cost ≈ 2–3¢ per photo on Sonnet 5.

**Migrations run by user (all success):** 016, 017.

**Still open:** Phase 4 — **Consolidate** guest prefs + Elise's recipe library → AI-generated **editable visual weekly menu** → **shopping list** scaled to pax (uses the now-configured Claude API; run heavy build agents on Opus 5). Also outstanding from before: Sean & Elise CRM logins; optional Cards & Bank / Balance Sheet pages; Phase 2b one-click Resend email invites (optional — copy-link works today); rotate the GitHub PAT (flag Rob).

---

## Session: July 29, 2026 (continued) — CRM UX polish + Preference Sheet pipeline (Phases 1 & 2a)

**Theme:** After go-live, a big feature/polish push on the Perpetual Blue CRM — calendar & nav UX, then built the guest Preference Sheet pipeline through Phase 2a (public guest self-service form). Work delegated to subagents; one stalled mid-task and was finished by hand. All pushes to `perpetual-blue-crm` succeeded (that repo's pushes aren't classifier-blocked; only `orion-memory` is).

### ⛵ Perpetual Blue CRM — feature work

**Calendar (commits `d7d0916`, `79c9265`, `89ff907`):**
- Charter tiles + list rows are now clickable → `/charters/[id]` (onClick+useRouter to preserve the diagonal half-day tile design; hover tooltip = guest name).
- Shows all 12 months of the season (removed the summer-hiding logic; dropped `monthHasCharters`).
- Under each month grid, a compact list of that month's charters (color dot + guest name + date range, clickable, timezone-safe formatting).

**Nav / lists:**
- Sidebar nav groups (Financials, Maintenance, Operations, Vessel) are now **collapsible** — chevron is a real toggle button; children render only when expanded; active group auto-expands (commit `085317f`).
- Charters list: **entire row clickable** (extracted to a client component `ChartersTable.tsx`; the old "View" link is now a visual cue) (commit `1eec0c7`).

**Preference Sheet pipeline (the big one):**
- **Phase 1** (commit `e3e5d45`) — `guest_preferences` table (**migration_013**), MULTIPLE sheets per charter (one per guest). Section on the charter detail page: add/edit/delete inline, allergies flagged in a red critical box, print route (`/charters/[id]/preferences/print`) with print-isolation CSS + auto-print. Components: `PreferenceSheetForm/View/Manager`, API routes under `app/api/charters/[id]/preferences/`. NOTE: the subagent stalled on an API error at the final wiring step — Orion finished the page fetch/render + the missing print route by hand.
- Extra fields (commit `55a41fe`, **migration_014**): `alcoholic_drinks_per_day` (None/1–2/3–4/5+) and `meal_portion_size` (Light/Medium/Heavy) as selects.
- **Phase 2a** — public guest self-service form (commit `60a46a5`, **migration_015**). Secret unguessable `preference_token` per charter → public page `/preference-form/[token]` (outside the (crm) group, no login) + public POST API. Security: charter resolved FROM THE TOKEN server-side (never body), strict field whitelist, service-role insert, insert-only, returns `{ok:true}` (no data echoed); page exposes only guest name + dates; `proxy.ts` allowlists the two public routes. Charter page gets a **Copy guest link** button + "X via guest form" received count. Built on Opus 5; security surfaces reviewed by Orion before deploy.
- Optional contact fields (commit `fa78c04`, **migration_016**): `guest_email` + `guest_phone` on both forms, whitelisted in the public API, shown as a contact line (mailto/tel) in view + print.

**Migrations run by user in Supabase (all success):** 013, 014, 015, 016.

**Rob to review (Phase 2a security trade-offs, all conscious/acceptable-for-now):** no rate limiting on the public submit; tokens never expire / no rotation UI yet; no CSRF (fine for a public session-less insert-only endpoint).

**Roadmap (see [project_perpetual_blue.md](project_perpetual_blue.md) → Provisioning Pipeline):** ⏭️ Phase 2b = one-click Resend email invites (needs Resend acct); Phase 3 = recipe library + photo→recipe AI; Phase 4 = consolidate → **editable visual weekly menu** → shopping list (Phases 3–4 need Anthropic key, run on Opus 5).

**Model preference set:** Opus 4.8 default, bump to Opus 5 when it helps — see [pref_model_opus5.md](pref_model_opus5.md).

---

## Session: July 29, 2026 — Perpetual Blue CRM Goes Live (3 bug fixes)

**Theme:** Created admin Auth account, linked it to the CRM, and squashed three separate bugs that were blocking login and navigation. CRM is now live and fully navigable.

### ⛵ Perpetual Blue CRM — Live (commits `6240769`, `ae59cb7`)

**Auth setup:**
- Created Supabase Auth user for Nick (`nick73patel@gmail.com`), UID `fae3ca42-a6c1-40ca-8e3d-12feb4a18e0c`
- The migration_010 seed row hadn't landed, so linked via upsert: `INSERT INTO crm_users (...) VALUES ('Nick','admin',...) ON CONFLICT (email) DO UPDATE SET supabase_uid = EXCLUDED.supabase_uid`
- Sean & Elise Auth accounts still TODO

**Bug 1 — `crm_users` RLS infinite recursion (500 on login):**
- migration_010 admin policies used inline `EXISTS (SELECT 1 FROM crm_users ...)` — a policy on crm_users that queries crm_users → Postgres 42P17 infinite recursion → 500.
- Fix: rewrote the 4 admin policies to use the `is_admin()` SECURITY DEFINER helper (bypasses RLS). Saved as `supabase/migration_012_fix_rls_recursion.sql`, applied manually in Supabase SQL editor.

**Bug 2 — Server components self-fetching their own API routes (500 after login):**
- Dashboard, Calendar, Alerts pages did `fetch('https://${host}/api/...')`. Server-side fetches carry no auth cookie → `proxy.ts` middleware redirected them to `/login` → fetch followed redirect to a 200 HTML page → `res.json()` threw on HTML → 500.
- Fix: extracted each route's query logic into `lib/data/{dashboard,calendar,alerts}.ts`, called directly from both the page (no HTTP) and a thin API-route wrapper. Routes re-export their types so existing `import type` lines keep working.

**Bug 3 — Sidebar/dashboard nav 404s:**
- Nav was scaffolded with a `/financials/*` and `/vessel/{profile,documents,subscriptions}` structure that was never built.
- Repointed to real pages: Financials → `/expenses`, `/tasks`, `/reconciliation`, `/analytics`; Vessel → `/vessel`, `/documents`, `/operations`. Dashboard quick-links fixed too.
- Dropped **Balance Sheet** (no page) and **Cards & Bank** (no page, though bank/cards data IS seeded — buildable later).

**Still TODO:**
1. Sean & Elise Auth accounts + linking SQL
2. (Optional) build Cards & Bank page + Balance Sheet page
3. Full click-through test of all 20 modules

**Security note:** GitHub PAT (`ghp_...`) is embedded in the git remote URLs for both `perpetual-blue-crm` and `orion-memory`. Not pushed to the repos (lives in local `.git/config`), but worth rotating + moving to a credential helper. Flag to Rob.

---

## Session: July 28, 2026 (continued) — CRM Deployment & Build Fix

**Theme:** Ran all 11 SQL migrations + 3 seed files in Supabase. Fixed Vercel build failures. Deployed CRM to Vercel. Set up custom domain DNS.

### ⛵ Perpetual Blue CRM — Deployed (commit `e724aca`)

**Live URL (pending DNS propagation):** crm.perpetualbluebvi.com
**Vercel project:** perpetual-blue-crm

**Migrations run in Supabase (all success):**
- fix_foundation_conflicts.sql → 001_schema → 003_vessel_ops → 004_crew → 005_maintenance → 006_itineraries → 007_tasks_surveys → 008_docs_ports → 010_roles → 011_fuel
- Seeds: seed_charters.sql (18 brokers + 19 charters), seed_expenses.sql (10 expenses), migration_002_seed.sql (vessels, accounts, bank, credit cards)

**Build fix:** Next.js 16 (Turbopack) rejected `next/headers` being imported via `lib/roles.ts` into a `'use client'` component (RoleProvider). Fixed by splitting roles into two files:
- `lib/roles.ts` — pure types + permission matrix (client-safe)
- `lib/roles-server.ts` — server-only functions (getCurrentCrmUser, requireEditPermission, requireAdmin)
- Updated ~40 API routes to import from `roles-server.ts`

**proxy.ts:** renamed from middleware.ts (Next.js 16 breaking change), function renamed to `proxy`

**DNS:** CNAME added in GoDaddy — `crm` → `2c5d4e4d92d7c459.vercel-dns-017.com.`

**Next steps (tomorrow):**
1. Create Supabase Auth accounts for Nick (nick73patel@gmail.com), Sean (sean@perpetualbluebvi.com), Elise (elise@perpetualbluebvi.com)
2. Run `UPDATE crm_users SET supabase_uid = '<uuid>' WHERE email = '...'` for each
3. Log in and test all 20 modules

---

## Session: July 28, 2026 — Perpetual Blue CRM Full Build

**Theme:** Built the entire Perpetual Blue Charter CRM from scratch using 13 parallel subagents. 20 modules, 175 files, 32,000+ lines of code. Pushed to GitHub. Multi-user login with 5-role RBAC designed and implemented.

### ⛵ Perpetual Blue CRM — Full Build (commit `3fab74d`)

**GitHub repo:** https://github.com/nick73patel-hash/perpetual-blue-crm (private)
**Stack:** Next.js 15 App Router · TypeScript strict · Supabase SSR · Supabase Storage · 10 migrations

**20 Modules completed:**
| # | Module | Notes |
|---|---|---|
| 1 | Dashboard | KPI tiles, upcoming charters, recent expenses, alerts panel |
| 2 | Charters | Full CRUD, season tabs, discrepancy flags, 19 historical charters seeded |
| 3 | Charter Calendar | Matches pb-calendar.html design exactly — diagonal half-day tiles, sea/amber CSS |
| 4 | Guests | Charter history, ILIKE search, preferences |
| 5 | Brokers | 18 brokers seeded, aggregated commission/revenue stats |
| 6 | Analytics | Pure CSS bar charts, broker leaderboard, season summaries |
| 7 | Expenses | Receipt upload (Supabase Storage), 10 expenses seeded incl. water weights, autopilot parts, Centennial Accounting |
| 8 | Maintenance + Schedule | 21 FP Sanya 57 tasks seeded, overdue highlighting, auto next-due recalculation |
| 9 | Inventory | Stock tracking, low-stock alerts |
| 10 | Crew + Payroll + Certs | Sean Powell + Elise McNabb seeded, cert expiry color badges |
| 11 | Captain's Log | Engine hours delta, charter linking, NM tracking |
| 12 | Fuel Log | Fill-up log, YTD gallons/cost, avg price/gal, Nanny Cay/Road Town seeded |
| 13 | Operations + Subscriptions | PYM/Starlink/MGH seeded, 30-day renewal alerts, vessel profile |
| 14 | Vessel + Fleet | FP Sanya 57 specs, insurance/CRVL expiry alerts |
| 15 | Itineraries + Rate Cards | 6 rate cards seeded (2025–2027), BVI 7-night template, Quote Calculator |
| 16 | Monthly Tasks | 19 task templates seeded, month navigator, progress bars |
| 17 | Post-charter Surveys | 5-KPI star ratings, rebook badge, submit endpoint (public) |
| 18 | Document Vault | Private Supabase Storage bucket, signed-URL downloads |
| 19 | Port Directory | 20 BVI/USVI/SVG/Grenada ports seeded, territory tabs, amenity icons |
| 20 | Bank Reconciliation | Monthly charter revenue vs expenses, Mercury balance tracker |
| + | Notifications + Alerts | Aggregates cert expiry, maintenance overdue, upcoming charters, subscription renewals |
| + | User Access (RBAC) | 5 roles: Admin (Nick), Owner Read-only (×2 partners), Captain (Sean), Crew (Elise) |

**Role access matrix:**
- **Admin (Nick):** full edit access to all 20 modules
- **Owner Read-only (2 partners):** see everything, zero edit buttons — safe viewing
- **Captain (Sean):** edit maintenance/captain's log/tasks/inventory; see charter totals + guest names; no financials/payroll
- **Crew (Elise):** calendar, charters, guests, itineraries, galley inventory (view + edit)

**Supabase project:** https://udmndiuasxgglqvskxua.supabase.co
**Target domain:** crm.perpetualbluebvi.com (Vercel deploy — next step)

**Migrations to run in Supabase SQL Editor (in order):**
001_schema → 001_foundation → 002_receipts_storage → 002_seed → 003_vessel_ops → 004_crew → 005_maintenance → 006_itineraries → 007_tasks_surveys → 008_docs_ports → 010_roles → 011_fuel

**Seeds to run after migrations:**
- `seed_charters.sql` (18 brokers + 19 charters)
- `seed_expenses.sql` (10 expenses)

### 🏔️ Condo Assistant — Mobile Photo Fix (commit `e21082c`)
- Root cause 1: `inspection-photos` bucket had no INSERT RLS policy → 403 silently swallowed
- Root cause 2: iOS page eviction when `capture="environment"` launches native camera
- Fix: removed `capture` attribute, immediate upload on file select, `alert()` on error, `migration_009` RLS policies
- User ran migration SQL: confirmed success ✅

### Carry-forward
- **PB CRM:** Run all migrations in Supabase SQL Editor, run seed files, deploy to Vercel at crm.perpetualbluebvi.com
- **PB CRM:** Create Supabase Auth accounts for Nick, Sean, Elise (+ 2 partners when ready), then link supabase_uid in crm_users table
- Condo Assistant Stripe billing (Step 3) — still pending
- Snow Mountain Ranch management proposal — draft when ready
- WPM Portal: start Phase 1 when SMR contract signed
- Perpetual Blue: confirm Form 8832 election decision with CPA before BVI BC formation

---

## Session: July 27, 2026 (continued — evening)

**Theme:** Condo Assistant — inspection checklist restructured room-based, multi-photo, edit button. Perpetual Blue — Grenada itinerary updated with Canouan SLYCR night.

### 🏔️ Condo Assistant — Inspection Overhaul (commit `202e06a`)

**Room-based checklist (`new/page.tsx` full rewrite):**
- Sections in order: Safety → Exterior → Living/Dining → Fireplace → Kitchen → Bedrooms → Bathrooms → Laundry → Garage → BBQ → General
- Safety: amber header, expandable smoke/CO detector entries (up to 4 each) with per-entry location + notes + checked state. Optional; renders first.
- Exterior door locks: expandable with + button (default "Front door"); add back door / side door etc.
- Bathroom: added "Check for mold" line item
- Garage: added "Spray lubricant on rollers and track" with green $5 billable badge; "Billable add-ons: $5" line at card bottom
- Optional sections (Fireplace, Garage, BBQ): N/A toggle preserved

**Multi-photo (`new/page.tsx` + `edit/page.tsx`):**
- `itemPhotos` state changed from `Record<string, File | null>` → `Record<string, File[]>`
- Camera button captures multiple photos per line item; thumbnails all shown with × to remove
- Upload loop iterates all files per key; DB `photo_url` stores JSON array string `'["url1","url2"]'`
- Edit page: parses existing `photo_url` — JSON array or legacy plain string on load; merges existing + new on save

**Edit button fix (`page.tsx`):**
- Was gated to `{insp.status === "in_progress" && ...}` — completed inspections had no edit access
- Fixed: button shows for all inspections; label `"Resume"` for in-progress, `"Edit"` for completed

### ⛵ Perpetual Blue — Grenada Itinerary Updated

Artifact updated (same URL): https://claude.ai/code/artifact/23c607f8-29df-4b6a-a4fe-0b5c081e3b82

**Change:** Dropped Aug 13 as a Grand Anse rest day. Inserted **Sandy Lane Yacht Club & Residences, Canouan** on Aug 15 (Day 4). Route is now:
- Day 1 (Aug 12): Grand Anse Bay → evening arrival
- Day 2 (Aug 13): BBC Beach + Black Bay stop → Bathway (was Day 3)
- Day 3 (Aug 14): Bathway → Tyrrel Bay, Carriacou (was Day 4)
- Day 4 (Aug 15): Sandy Island + Sculptures → Union Island customs (into SVG) → SLYCR Canouan ★
- Day 5 (Aug 16): Canouan → Salt Whistle Bay, Mayreau (~12nm south)
- Days 6–10: unchanged (Tobago Cays × 2, Tyrrel Bay, Grand Anse, depart Aug 21)

SVG customs note: now clears in at Union Island on Day 4 (before Canouan), out on Day 8.

**Additional fixes (late session):**
- Vercel builds silently blocked since `d2f21b6` — `isMaintenanceRequest`/`sendMaintenanceAlertEmail` added to chat route but not mocked in tests → TypeError → 500 → all deploys blocked. Fixed in `1037b56`.
- Multi-photo: subagent left old single-file `CameraButton`. Re-implemented as `PhotoInput` (File[] array, append on each tap, all thumbnails shown). Commit `d4eb468`.
- Karim save failure: `migration_008` not run in Supabase → `photo_url` column missing → every inspection save blocked with 500. API hardened to omit `photo_url` from insert when null (commit `c1c9805`). User ran migration; confirmed working. ✅

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending
- PB website + all 45 to-do items from artifact
- Snow Mountain Ranch management proposal — draft when ready
- WPM Portal: start Phase 1 when SMR contract signed
- Perpetual Blue: confirm Form 8832 election decision with CPA before BVI BC formation
- 274 Lions Gate Dr — verify bedroom count (3 or 4) with property record
- **Perpetual Blue CRM** — needs API keys pasted into `.env.local`, run migrations 001 + 002, launch 6 parallel build agents

---

## Session: July 27, 2026

**Theme:** Condo Assistant — bug fixes, Karim inspection features (camera + Spanish toggle), guest double-send fix, notification system overhaul.

### 🏔️ Condo Assistant — Bug Fixes

**Login page hang (fixed):**
- Root cause: `router.push("/admin")` is client-side navigation — doesn't send session cookies to server. Middleware couldn't see the Supabase session until manual refresh.
- Fix: replaced with `window.location.href = "/admin"` in `app/admin/login/page.tsx`. Also removed unused `useRouter` import. Commit `ea08fd2`.

**Guest chat double-send (fixed):**
- Root cause: Chrome fires a final `onresult` event after `stop()` is called on continuous speech recognition (buffered audio). This re-populated the transcript and started a second silence timer → second `sendMessage` call 2s later.
- Fix 1: `hasSentRef` — set to `true` before sending; `onresult` handler checks it and ignores late events.
- Fix 2: `isSendingRef` — ref-based guard in `sendMessage` (not React state) so stale closures can't slip through.
- File: `app/guest/[token]/page.tsx`. Commit `146c798`.

**Vercel build failure (fixed):**
- Root cause: subagent added `// @ts-expect-error` above `capture` attribute, but TypeScript already accepts `capture` on file inputs. An unused `@ts-expect-error` is itself a build error in strict mode.
- Fix: removed the comment. Commit `441df1d`.

### 🏔️ Condo Assistant — Karim Inspection Features

**Camera icon per line item:**
- Camera button added to the right of every Notes field across all section types (static, bedroom, bathroom) in `app/admin/properties/[id]/inspections/new/page.tsx`.
- On mobile: `capture="environment"` opens rear camera directly. Selecting a photo shows thumbnail with X to clear.
- On submit: photos upload to Supabase Storage bucket `inspection-photos` before API call; public URLs stored in `inspection_items.photo_url`.
- View page (`[inspectionId]/page.tsx`): clickable thumbnail renders next to notes when `photo_url` is present.
- API route (`app/api/inspections/route.ts`): `photo_url` included in bulk insert.
- Migration: `supabase/migration_008_inspection_photos.sql` — `ALTER TABLE inspection_items ADD COLUMN IF NOT EXISTS photo_url TEXT;` (run in Supabase SQL editor). Storage bucket `inspection-photos` created (Public: ON).

**Spanish → English toggle:**
- "Translate to English" button added to inspection view header (`[inspectionId]/page.tsx`).
- Calls new `app/api/translate/route.ts` — free MyMemory API, no API key required.
- One-tap translates all notes fields; tap again reverts to original Spanish. Spinner shown during request.
- Commit: `58a306b`.

**Summer inspection season:**
- Added `"summer"` option to inspection new/view pages and DB constraint. Migration `migration_007`. Commit `e0db369` (prior session, logged here for completeness).

### 🏔️ Condo Assistant — Notification System Overhaul

**Problem:** maintenance requests (clogged toilet, broken appliance, leak, etc.) triggered zero notifications. Towel SMS had empty subject (Resend rejection). Unanswered questions got email only.

**Fixes in `lib/email.ts` and `app/api/chat/route.ts` (commit `d2f21b6`):**
- `sendSMS()` subject fixed: `""` → `"WPM Alert"`.
- Added `isMaintenanceRequest()`: detects AI responses mentioning an issue keyword (clog, leak, broken, HVAC, pest, appliance, wifi, etc.) AND an action keyword (contacting, notify, arrange, etc.).
- Added `sendMaintenanceAlertEmail()`: red-header email to both admins + SMS to both Verizon numbers (3035200562, 7202348172 via `@vtext.com` gateway).
- `sendUnansweredQuestionEmail()`: now also sends SMS after email.
- `chat/route.ts`: new `isMaintenanceRequest` check calls `sendMaintenanceAlertEmail` before the existing towel check.

**SMS trigger summary (all send to both Verizon numbers):**
- Maintenance request → email + SMS ✅ (new)
- Towel request → email + SMS ✅ (was broken, now fixed)
- Unanswered question → email + SMS ✅ (SMS is new)

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending
- PB website + all 45 to-do items from artifact
- Snow Mountain Ranch management proposal — draft when ready
- WPM Portal: start Phase 1 when SMR contract signed
- Perpetual Blue: confirm Form 8832 election decision with CPA before BVI BC formation
- 274 Lions Gate Dr — verify bedroom count (3 or 4) with property record
- **Perpetual Blue CRM** — foundation built; needs API keys pasted into `.env.local`, then run migrations 001 + 002, then launch 6 parallel build agents

---

## Session: July 26, 2026

**Theme:** Condo Assistant — native inspection checklist system built end-to-end for Karim; all 60 properties fully enriched.

### 🏔️ Condo Assistant — Inspection Checklist System (Karim)

Built a full native inspection module — no third-party app, zero new dependencies, fully integrated into Condo Assistant.

**Database (migrations run in Supabase):**
- `migration_005_inspections.sql` — added `inspector` role to `org_users`, created `inspections` table (id, org_id, property_id, inspector_name, season, status, completed_at) and `inspection_items` table (inspection_id, section, item_label, repeat_index, checked, notes, sort_order).
- `migration_006_property_room_counts.sql` — added `bedroom_count` and `bathroom_count` columns to `properties`.

**Auth:**
- Added `requireOrgOrInspector()` and `getUserRole()` helpers to `lib/auth-helpers.ts`. Inspector role has scoped access to `/admin/inspector` only.
- Created Karim's login: `maintenance@winterparkmanagement.com`.

**API routes:**
- `app/api/inspections/route.ts` — GET (list by property, includes item counts) + POST (create inspection + bulk insert all items).
- `app/api/inspections/[id]/route.ts` — GET (full detail with items) + PATCH (status completion, item updates).

**UI pages:**
- `app/admin/inspector/page.tsx` — inspector-scoped dashboard listing all properties.
- `app/admin/properties/[id]/inspections/page.tsx` — inspection history with season, date, inspector, status badge, % complete.
- `app/admin/properties/[id]/inspections/new/page.tsx` — full 14-section checklist form with dynamic bedroom/bathroom sections, N/A toggles on optional sections (Fireplace, Furniture, Garage, BBQ), Save Progress and Complete Inspection buttons.
- `app/admin/properties/[id]/inspections/[inspectionId]/page.tsx` — read-only detail view.
- `app/admin/properties/[id]/inspections/[inspectionId]/print/page.tsx` — print/PDF export with Unicode checkboxes, auto window.print() trigger. Zero new packages.

**Bedroom section items:** Inspect bed frame and slats · Check mattress condition · Clean and check ceiling fan · Check closet doors · Inspect closet rods and shelves · Check window treatments · Check outlets and light switches · Inspect baseboards and trim.

**Room counts bulk-loaded:**
- First pass: 38 properties populated from existing DB descriptions via Node.js admin script.
- Second pass: scraped all 5 pages of bookwinterparkmanagement.escapia.com — 24 more properties updated. All 60 properties fully configured.

**Property descriptions bulk-updated:**
- 22 properties had empty descriptions. Bulk-updated all with Escapia listing copy (BR/BA/sleeps in the lead) via Node.js admin script. All 60 properties now fully enriched.

**Note:** 274 Lions Gate Dr — Escapia lists 3BR/3BA; written description says "4-bedroom." Set to 3BR/3BA (trusting structured data). Flag to confirm with owner.

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending
- PB website + all 45 to-do items from artifact
- Snow Mountain Ranch management proposal — draft when ready
- WPM Portal: start Phase 1 when SMR contract signed
- Perpetual Blue: confirm Form 8832 election decision with CPA before BVI BC formation
- 274 Lions Gate Dr — verify bedroom count (3 or 4) with property record
- **Perpetual Blue CRM — foundation agent kicked off (see below)**

### ⛵ Perpetual Blue CRM — Build Plan Locked

Full CRM designed for Perpetual Blue — standalone Next.js + Supabase app, same stack as Condo Assistant.

**12 modules:** Vessel & Fleet · Charter Calendar (integrate existing) · Financial Management · Monthly Task Checklist · Credit Cards & Bank Reconciliation · Guest Management + Preference Forms · Maintenance Log · Inventory · Itineraries & Quoting · Crew & Payroll · Broker Management · Operations & Services (Starlink/subs, fuel, captain's log)

**8 additions I proposed:** Crew cert expiry tracker · Fuel log · Document vault · Post-charter guest survey · Annual maintenance schedule · Receipt photo capture · Port & marina directory · Rate card manager

**Build strategy:** Foundation agent first (schema, auth, vessel model, project setup — ~1 hr), then 6 parallel agents owning 2 modules each (~2–2.5 hrs), then integration pass (~45 min). Target: 4 hrs wall-clock.

**Artifact:** https://claude.ai/code/artifact/323aaf61-706b-482c-89db-a98da3ba3627

**Status:** Foundation complete. Supabase project created (udmndiuasxgglqvskxua). Stopped before pasting API keys — resume tomorrow.

**Resume checklist:**
1. Open `.env.local` in `perpetual-blue-crm`
2. Paste 3 keys from Supabase → Settings → API Keys → "Legacy anon, service_role API keys" tab: URL = `https://udmndiuasxgglqvskxua.supabase.co`, anon key, service_role key
3. Run `supabase/migration_001_foundation.sql` in Supabase SQL Editor
4. Run `supabase/migration_002_seed.sql`
5. Launch 6 parallel agents
6. Also review where Condo Assistant API keys are stored

---

## Session: July 24, 2026

**Theme:** Condo Assistant — guest link inline editing; Perpetual Blue — BVI charter yacht tax structure research.

### 🏔️ Condo Assistant

- **Guest links now editable after creation.** Pencil icon on each link card opens inline edit form pre-populated with current values. All 5 fields editable: guest name, door code, common area code, check-in date, departure date. PATCH API extended to accept all fields conditionally (toggle-active still works unchanged). Empty string coerces to null to allow clearing codes. Committed (ed890fd) and pushed to production.

### ⛵ Perpetual Blue — BVI Tax Structure Research

Researched two ownership structures for 3 US citizens operating a BVI crewed charter:
- **Scenario A:** 3 US citizens → Florida LLC → BVI LLC → charter vessel
- **Scenario B:** 3 US citizens → BVI LLC directly → charter vessel

**Key findings:**
- Both produce identical US federal tax results. FL LLC middle layer adds $3–10K/year in cost with zero benefit. **Scenario B wins.**
- BVI LLC defaults to CFC (Controlled Foreign Corporation) for US tax purposes — both scenarios.
- Charter income to unrelated parties likely escapes Subpart F. NCTI/GILTI applies to profits.
- **At-or-below breakeven (our situation):** negligible tax exposure. $60K retained → ~$2,500/person at 12.6% rate.
- **Annual compliance cost: ~$1,500–3,000** (BVI registered agent + government fee). All IRS forms (5471, FBAR, 8938, 8992) are free to file yourself.
- FBAR: free online at bsaefiling.fincen.treas.gov.
- Form 5471 is complex but DIY-able — get one-time CPA walkthrough in year 1, then handle in-house.
- BVI Economic Substance Act: charter yachts explicitly excluded (BVI ITA 2023 guidance). No BVI substance requirements.
- No US-BVI tax treaty. BVI 0% corporate tax = no foreign tax credits (but also no BVI tax).
- **Key formation decision:** file Form 8832 (check-the-box) to elect partnership vs. default CFC — do before entity begins operating.
- File Form 926 when yacht transfers into BVI BC (yacht value >$100K).
- Updated `project_perpetual_blue.md` with full tax structure section + 4 new action items.

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending
- PB website + all 45 to-do items from artifact
- Snow Mountain Ranch management proposal — draft when ready
- WPM Portal: start Phase 1 when SMR contract signed
- Perpetual Blue: confirm Form 8832 election decision with CPA before BVI BC formation

---

## Session: July 23, 2026

**Theme:** Perpetual Blue — HeySea research saved to disk; Yacht management proforma; Condo Assistant — checkout calendar UX fix; Snow Mountain Ranch property management research.

### ⛵ Perpetual Blue

- **HeySea Seaview 60 quality research brief** saved to disk (`perpetual-blue/heysea_quality_research.md`) and pushed to GitHub. Full comparison vs. Fountaine Pajot and Bali: hull construction, CE Cat A Ocean cert, Bill Dixon pedigree, BVI charter market acceptance, resale risk, CRVL licensing, insurance questions, 10-point due diligence checklist.
- **Proforma files confirmed built:** `HeySea60_QuarterShare_Proforma.xlsx` (4 sheets: Assumptions, Owner Economics, Mgmt Revenue, Operational P&L), `Yacht_Mgmt_Proforma.xlsx` (1/5/10/20 yacht scenarios).
- **Charter rates benchmarked:** $38K low / $52K high (10% below ATLAS, the operating BVI HeySea 60 at $42K–$58K/week).
- **Open item:** CRVL domestic vs. foreign rate — whether BVI-flagged, BVI-based vessel owned by FL LLC qualifies for domestic (lower) rate. ABM Group to confirm.

### 🏔️ Condo Assistant

- **Checkout calendar UX fix:** When check-in date is selected, departure date auto-populates to the next day (so native date picker opens to correct month). If check-in is last day of month, departure jumps to 1st of following month. Added `min` attribute to block selecting departure on/before check-in. File: `app/admin/properties/[id]/links/page.tsx`. Committed and pushed (4386b6f).

### 🏠 Snow Mountain Ranch (new WPM opportunity)

- 43 staff units, 12-month leases, each employee pays separately monthly.
- Property management software evaluated: Buildium (~$55/mo, 150 units), Rentec Direct (~$45/mo, 100 units), DoorLoop ($270/mo for 43 units — too expensive), Cozy (free, bare bones).
- **Recommendation:** Rentec Direct or Buildium — flat fee covering all 43 units, ACH tenant portal, professional PM company features.

### Condo Assistant — UX Fixes
- **Checkout calendar auto-scroll:** When check-in date is picked, departure auto-populates to next day so calendar opens to correct month. Last day of month → departure jumps to 1st of following month. Committed and pushed (4386b6f).
- **Date picker click area:** Both check-in and departure date inputs now open calendar picker on any click anywhere in the box (showPicker() on click). Committed and pushed (cf136b2).

### Snow Mountain Ranch (New WPM Opportunity)
- YMCA of the Rockies, Granby CO — 43 staff units, 12-month leases, each employee pays separately.
- PM software evaluated: Buildium ($55/mo), Rentec Direct ($45/mo), DoorLoop ($270/mo for 43 — too expensive), Cozy (free).
- **Recommendation:** Rentec Direct or Buildium when contract is signed.

### WPM Property Management Portal — Full Build Plan
- Decision: build custom CRM to replace Buildium/Rentec. Own it forever. ~20 hours with Orion.
- 5-phase plan: Foundation → Payments + SMS → Maintenance → Bookkeeping → Staff Housing Features.
- **Key differentiators identified (research-backed):** SMS reminders (no competitor has them), flexible custom reports (biggest complaint), maintenance calendar + recurring tasks, staff/employer split billing + payroll deduction export (nobody builds this — real gap in resort/hospitality markets).
- Tech stack: Next.js + Supabase + Tailwind + Stripe + Plaid + Twilio. Running cost ~$45/mo vs. $45–$280/mo SaaS.
- **Trigger to start:** Snow Mountain Ranch management contract signed → begin Phase 1 + submit Stripe/Plaid applications same day.
- Full plan saved: `C:\Users\ducat\Projects\wpm-portal-plan.md`
- Build plan artifact published: https://claude.ai/code/artifact/6ccd6a1f-3121-4441-a757-67826e5afe42
- Memory file created: `project_wpm_portal.md`

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending
- PB website + all 45 to-do items from artifact
- PYM dispute: reconcile double-count flags before finalizing combined total
- Pull itemized receipt for PYM13459 ($1,268.56 bundled Aug 2024)
- Snow Mountain Ranch management proposal — draft when ready
- WPM Portal: start Phase 1 when SMR contract signed (submit Stripe + Plaid apps same day)

---

## Session: July 20, 2026

**Theme:** Perpetual Blue — R&M Supplies ledger cross-reference, updated category reports, dinghy 2026 report, buyer inquiry Q&A.

### ⛵ Perpetual Blue — Maintenance Analysis (PYM Dispute Support)

**New document processed:** R&M Supplies Transactions (Account 574 — Xero), 15 pages, $49,647.47 net, covering Jul 2021–Jul 2026. Saved to scratchpad alongside original 183-page R&M transactions document.

**Key discovery:** `PYMSF2846` ($18,515.98 — Nov 2023, "Parts for Air con replacement") existed ONLY in the Supplies ledger — entirely absent from the main R&M document. This is the largest single entry in the Supplies doc and pushes the AC category total from $26,143.49 → **$44,670.47**.

**All 5 category reports updated with Supplies doc entries:**

| Category | Prior Total | Added | Updated Total |
|---|---|---|---|
| Engines & Drivetrain | $158,109.75 | +$5,209.67 | $163,319.42 |
| Batteries & Electrical | $72,716.25 | +$1,887.76 | $74,604.01 |
| Air Conditioning | $26,143.49 | **+$18,526.98** | **$44,670.47** |
| Sails & Rigging | $24,499.86 | +$764.01 | $25,263.87 |
| Generator | $22,764.25 | +$320.83 | $23,085.08 |
| Gelcoat/Painting/Hull | $32,932.61 | $0 | $32,932.61 |
| **COMBINED** | **$337,166.21** | **+$26,709.25** | **$363,875.46** |

**Watermaker identified as new category:** $135.03 (Rainman impeller kits + membrane filter from Supplies doc).

**Flags requiring owner action:**
- PYM13459 ($1,268.56 bundled Aug 2024) — Yanmar + Onan + supplies mixed invoice, needs itemized receipt to split
- PYM1775 ($961.50) appears in both Engine AND Generator reports — possible double-count, verify in Xero
- VI Custom Refits #00285 rudder work ($5,927.05) appears in both Engine and Gelcoat reports — assign to one category only
- Unconfirmed oil purchases ($742.57 across 8 entries) — engine or generator, need receipts
- Battery pk27/PYM2184 ($202.96) — starter battery, confirm category (Engine vs Battery)

**Additional reports produced:**
- **Gelcoat, Painting & Hull Cleaning** ($32,932.61) — clean formatted version with 3 groups: Hull Cleaning ($1,397.16), Haul-Out/Bottom Paint 2025 ($14,511.90), Gelcoat & Topside Repairs 2026 ($17,023.55)
- **Dinghy Repairs & Maintenance 2026** — $3,375.38 confirmed 2026 / $4,886.32 including Nov 2025 carry-over. Key finding: davit strongpoint failure caused cascade damage to outboard cowl — $850+ in preventable downstream repairs

**Buyer inquiry Q&A:** Subagent dispatched to answer 17 buyer questions (engines, generator, rigging, electrical, watermaker, electronics, history, logistics) from both maintenance documents. Pending at session save.

**Buyer Q&A Word document created:** `perpetual_blue_buyer_qa.docx` — 17 questions across 8 sections (Layout, Engines, Generator, Rigging & Sails, Electrical, Watermaker & Electronics, History & Condition, Logistics). Saved to `C:\Users\ducat\Projects\perpetual-blue\perpetual_blue_buyer_qa.docx`. Professional Navy/blue styling, vessel summary table, running header, page numbers, disclaimer. 4 items flagged for captain confirmation (engine hours, panel count, MultiPlus model, radar/AIS inventory).

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending
- PB website + all 45 to-do items from artifact
- PYM dispute: reconcile the double-count flags before finalizing combined total
- Pull itemized receipt for PYM13459 ($1,268.56 bundled Aug 2024)
- Set up git repo for memory files + Projects/perpetual-blue so "back up and push" works (no git remote currently configured)

---

## Session: July 18, 2026 (continued — afternoon)

**Theme:** Condo Assistant — Trailhead 414 unit built, dual-admin email, towel request SMS alerts.

### 🏔️ Condo Assistant

- **Dual-admin email for unanswered questions.** Unanswered question alerts now go to both `nick73patel@gmail.com` AND `admin@winterparkmanagement.com`. Added `ADMIN_EMAILS` constant + shared `sendEmail()` helper in `lib/email.ts`. Commit `b5ab647`.

- **Towel request detection + email alerts.** New `isTowelRequest()` function detects when AI confirms it's notifying management about a towel request (checks for "towel" + action phrases in AI response). New `sendTowelRequestEmail()` sends teal-colored alert email to both admins. Wired into `app/api/chat/route.ts`. Tests updated, 204/204 pass.

- **Free SMS via Verizon email-to-SMS gateway.** When a towel request fires, also sends a plain-text SMS to `3035200562` and `7202348172` via `@vtext.com` gateway (zero extra cost, uses existing Resend). SMS failure is non-fatal. Commit `3cb8acd`.

- **Towel detector broadened.** Initial detection only caught "contact/contacting/let...know". AI was using "reach out" — missed. Extended trigger list to include: "reach out", "notify", "inform", "arrange". Live-tested and confirmed. Commit `c302a4a`.

- **Trailhead 414 unit built.** Full property record created in Supabase under Trailhead Lodge building:
  - Address: 401 Trailhead Circle, Unit 414, Winter Park CO 80482
  - WiFi: Trailhead414 / 401trailhead · Sleeps 6 · Type: condo · Door code: 3845
  - Full check-in instructions (driving directions, parking passes, pool card) + checkout instructions
  - 20 unit-level Q&A pairs with OpenAI embeddings covering: address, directions, door code, parking, pool card, laundry, BBQ, fireplace, no A/C, max guests, kitchen supplies, house rules, fire restrictions, trash, floor/building, grocery delivery, rental discounts, restaurants
  - Property ID: `9d225449-a6f8-4b2e-8fe3-3919d73a7e1b`

### Carry-forward
- Condo Assistant Stripe billing (Step 3) — still pending
- PB website + all 45 to-do items from artifact

---

## Session: July 18, 2026 (morning)

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

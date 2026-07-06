# Project 4 — Rent Cars

## What this is
A car rental business. Details TBD — could be peer-to-peer (like Turo), a traditional fleet rental, or a hybrid model targeting specific markets (resort towns, airports, corporate, etc.).

## Status
🟡 Starting — concept defined, no work started yet

## Partners
- Christian
- Eduardo
- Hami

## Fleet (confirmed)
- **911 Carrera Cabriolets** — 10 to 20 years old, both PDK and manual transmissions
- **356 Spyders** — sourced from the vintage car market
- **Toyota 4WD Minivans** — family-friendly, practical for larger groups; snow tires + roof boxes for winter, camping gear for summer
- **Mazda Miatas** — fun, affordable open-top option
- **C8 Corvette** — high-performance American sports car, premium rental segment
- **Land Rover Defender 130** — 8-seater, lifted, snow tires, ski box, purpose-built Colorado mountain vehicle
- **Lotus Emira** — V6 automatic (Toyota engine), exotic mid-engine sports car, premium rental segment
- **C7 Corvette** — more attainable price point than C8, still a head-turner
- **Jeep Wrangler** — iconic off-road, top-off in summer, snow-capable in winter, huge rental appeal
- **Toyota Tacoma** — bulletproof reliability, great for adventure/camping trips, strong resale
- **Toyota Land Cruiser** — legendary reliability, premium SUV, holds value exceptionally well
- **Lexus GX 550** — 7-passenger luxury SUV, off-road capable, strong brand appeal for families and ski groups
- **Toyota Supra** — Japanese sports car icon, BMW B58 engine, premium-but-attainable segment

## Fleet (under consideration)
- **Lexus LX 600** — flagship full-size luxury SUV, 7-passenger. Higher price point, premium market.
- **Ineos Grenadier** — purpose-built off-road utility vehicle, spiritual successor to the classic Land Rover Defender. Manual and automatic, extremely capable on dirt/snow, strong visual presence. Good fit for Colorado mountain/adventure market.
- **2012 Mercedes R-Class** — 7-passenger AWD luxury wagon/van. $10K acquisition (~60K miles). Winter: studded snow tires + ski box. Summer: full camping setup. Target: $150/day, 150 days/yr. ⚠️ Verify air suspension vs coil spring before buying — air suspension replacement is $1,500–3,000; coil spring model is far cheaper to maintain.

## Financial Analysis (May 2, 2026)
Assumptions: 0 down, 6% financing over 5 years, $2K/yr insurance, $8K/yr maintenance, $2K/yr misc

| Vehicle | Car Cost | Rate/day | Days/mo | Net/yr (loan) | Net/yr (post-loan) |
|---|---|---|---|---|---|
| 911 Carrera | $70K | $335 | 12 | $20,000 | $36,240 |
| 911 Carrera | $60K | $300 | 12 | $17,280 | $31,200 |
| 911 Carrera | $60K | $335 | 13 | $26,340 | $40,260 |
| Defender 130 | $60K | $275 | 12 | $13,780 | $27,700 |
| Defender 130 | $60K | $325 | 12 | $20,980 | $34,900 |
| Defender 130 | $60K | $325 | 15 | $32,680 | $46,600 |
| Toyota Supra | $50K | $235 | 12 | $10,240 | $21,840 |
| Toyota Supra | $50K | $250 | 14 | $18,400 | $30,000 |
| Lexus LX 600 | $95K | $425 | 12 | $27,160 | $49,200 |
| Lexus LX 600 | $95K | $475 | 14 | $45,760 | $67,800 |

## Key questions to answer
- What is the model? (own fleet, peer-to-peer marketplace, franchise?)
- What market/location? (Winter Park area, Denver, broader Colorado?)
- How do reservations, payments, and handoffs work?
- Insurance and liability structure?
- How can AI help? (dynamic pricing, fleet management, customer support bot)

## How Gerty can help
- Build a booking and reservation system
- Dynamic pricing engine based on season/demand
- Fleet management dashboard
- Customer-facing AI concierge for rental questions
- Automated check-in/check-out workflows

## European Car Sourcing — Listing Sites
For sourcing 911 Cabriolets, 356 Spyders, and other European fleet vehicles from Europe:

1. **[mobile.de](https://www.mobile.de/?lang=en)** — first stop. Germany has the deepest Porsche market in the world: best selection, best prices, best documentation (German owners keep meticulous records). English UI available.
2. **[AutoScout24](https://www.autoscout24.com/)** — second sweep. Largest pan-European marketplace, ~2.5M listings across 18+ countries. Picks up listings in NL, BE, IT, ES that mobile.de misses.
3. **[Classic-trader.com](https://www.classic-trader.com/)** — purpose-built for vintage. Better than mainstream sites for 356 Spyders and other classics.
4. **[mobile.de classic / Oldtimer section](https://suchen.mobile.de/auto/oldtimer.html)** — 25+ year old vehicles filter. Useful because cars 25+ years old are exempt from US EPA/DOT federalization rules — much easier to import.

### Import note
PDK 911 Cabriolets started ~1989, so the older PDK cars are now past the 25-year US import threshold — fair game without federalization headaches.

## Financial Analysis — Ineos Grenadier (May 22, 2026)
Assumptions: 0 down, 6% financing over 5 years, $2K/yr insurance, $3K/yr maintenance (bumped from $2K — limited US dealer network), $2K/yr misc

| Vehicle | Car Cost | Rate/day | Days/mo | Net/yr (loan) | Net/yr (post-loan) |
|---|---|---|---|---|---|
| Grenadier | $75K | $275 | 12 | $15,200 | $32,600 |
| Grenadier | $75K | $325 | 12 | $22,400 | $39,800 |
| Grenadier | $75K | $325 | 15 | $34,100 | $51,500 |
| Grenadier | $80K | $350 | 14 | $33,200 | $51,800 |

Notes:
- Sweet spot: $325/day — less than Defender 130 but niche appeal justifies premium over generic 4x4
- Dealer network: Ineos US coverage is thin; Denver has one location — factor in for maintenance logistics
- Resale: limited production + strong enthusiast following = holds value well
- Complements Defender 130 well: Defender = family/luxury, Grenadier = adventure/rugged — different clientele, same market
- Best scenario: $325/day at 15 days/mo = $34K/yr during loan, $51.5K post-loan

## Financial Analysis — 2012 Mercedes R-Class (June 15, 2026)
Assumptions: $10K cash purchase (no financing), 150 rental days/yr @ $150/day

| Vehicle | Car Cost | Rate/day | Days/yr | Gross Revenue | Annual Costs | Net/yr | Payback |
|---|---|---|---|---|---|---|---|
| Mercedes R-Class | $10K cash | $150 | 150 | $22,500 | $5,800 | **$16,700** | ~7 months |

**Annual cost breakdown:**
- Insurance: $1,500 (low — low vehicle value)
- Maintenance: $2,500 (conservative — aging Mercedes parts are not cheap)
- Snow tires + ski box (amortized): $500
- Camping setup (amortized): $300
- Misc / cleaning / platform fees: $1,000
- **Total: $5,800/yr**

**Notes:**
- ROI is exceptional at 167% yr 1 — low acquisition cost does the heavy lifting
- R-Class strengths as rental: AWD, 7-passenger, massive cargo area, highway comfort, unique/memorable
- Watch out for: air suspension (expensive to repair if equipped), Mercedes aging maintenance costs
- Check whether it has air suspension before buying — coil spring models are far cheaper to maintain
- Segments well as a "ski family hauler" in winter and "adventure basecamp" in summer — different from rest of fleet
- At $10K, even 1–2 big repairs don't kill the economics

## Session Log
### Session: April 9, 2026
- Project added to directory
- Awaiting details from user and partners

### Session: May 8, 2026
- Logged European car-sourcing site shortlist (mobile.de, AutoScout24, Classic-trader, mobile.de Oldtimer)
- Noted 25-year US import rule applies to older PDK 911 Cabriolets

### Session: June 15, 2026
- Added 2012 Mercedes R-Class to fleet consideration: $10K cash, $150/day, 150 days/yr = $16,700 net/yr, ~7-month payback
- Note: verify air suspension vs coil spring before purchase

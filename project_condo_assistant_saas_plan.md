---
name: Condo Assistant — SaaS Build List & Sales/Marketing Plan
description: Full technical build roadmap and 12-tactic go-to-market plan for scaling Condo Assistant into a SaaS product
type: project-plan
originSessionId: 4b1f46b8-ae65-4aa9-9143-8196a1a66fa0
---
# Condo Assistant — SaaS Master Plan

**Target ARR:** ~$2.88M
- Individual owners: 5,000 clients × $40/mo = $2.4M ARR
- PM companies: 200 clients × $99–$499/mo avg = ~$480K ARR

---

## Part 1: SaaS Technical Build List

### Phase A — Foundation (Do first — nothing else works without this)

**1. Multi-Tenancy Architecture**
- Add `organizations` table to Supabase (id, name, owner_id, plan, stripe_customer_id, created_at)
- Add `org_id` foreign key to: properties, qa_pairs, guest_links, chat_messages, buildings
- Row-level security (RLS) policies: users only see their org's data
- Invite system: `org_members` table (org_id, user_id, role: owner/admin/viewer)
- Complexity: High — this is the most critical piece. Get it right first.

**2. Self-Service Signup & Onboarding Wizard**
- Public signup page at `/signup` (email → password → org name → property count → plan)
- Wizard flow: account creation → add first property → add knowledge base Q&A → generate first guest link
- Welcome email via Resend on signup
- Complexity: Medium

**3. Stripe Billing Integration**
- Pricing tiers:
  | Tier | Price | Properties | Who |
  |---|---|---|---|
  | Solo Host | $40/mo | Up to 3 | Self-managing individual owners |
  | Pro | $99/mo | Up to 20 | Small PM companies |
  | Agency | $299/mo | Up to 100 | Mid-size PM companies |
  | Enterprise | $499+/mo | Unlimited | Large PM companies, white-label |
- Annual billing option: 20% discount (improves cash flow, reduces churn)
- Stripe Checkout for new signups
- Stripe Customer Portal for self-serve plan changes, payment updates, cancellations
- Webhooks: handle subscription created/updated/canceled/payment failed
- Complexity: Medium-High

**4. Plan Enforcement**
- Middleware checks org's plan before allowing property creation
- Show upgrade prompt when limit is hit
- Grace period: 3 days after payment failure before locking account
- Complexity: Low-Medium

### Phase B — Product Depth (Do after billing is live)

**5. White-Label Branding Per Tenant**
- Each org can upload: logo, set primary color, set assistant name (e.g., "Jasper" → "Max")
- Branding applied to guest chat page (`/guest/[token]`)
- Custom domain support (CNAME to assistant.winterparkmanagement.com equivalent)
- Complexity: Medium

**6. Custom FROM Email Per Tenant**
- Each org connects their own Resend domain (or uses shared sending domain)
- Guest notification emails come from their brand, not from winterparkmanagement.com
- Complexity: Medium

**7. Admin Super-Dashboard (Internal)**
- Internal-only page at `/superadmin` (locked to Anthropic/Nick account only)
- Shows: total orgs, MRR, new signups this month, churn rate, at-risk accounts (payment failed)
- All organizations list with plan, property count, last active date
- Support flag system: mark accounts needing follow-up
- Complexity: Medium

**8. Tenant Team Management**
- Org owner can invite team members by email
- Roles: Owner (full), Admin (manage properties/KB), Viewer (read-only)
- Team members see only their org's data
- Complexity: Medium

### Phase C — Growth (Do when you have 50+ customers)

**9. Marketing / Landing Page**
- Single conversion-optimized page at `condo-assistant.com` (or similar domain)
- Above the fold: one headline (pain point), one subheadline (solution), one CTA button ("Start free trial")
- Sections: How it works (3 steps) → Demo video embed → Pricing table → Testimonials → FAQ → Footer CTA
- NO feature dump — one pain point, one outcome, one ask
- Complexity: Low (design-heavy, not code-heavy)

**10. Legal & Compliance**
- Terms of Service (can use Termly.io or Clerky to generate)
- Privacy Policy (required for GDPR/CCPA — you will have EU customers)
- Cookie consent banner
- GDPR: data deletion on request, data export endpoint
- Complexity: Low (legal template + simple endpoints)

---

## Part 2: Sales & Marketing Plan

### Phase 1 — Proof of Concept (Months 1–2)
**Goal: Sign up 10 paying customers. Validate the product.**

**Tactic 1 — Start with warm referrals (Rob & Eliot)**
- Rob and Eliot are the first case study — get their testimonial on video
- Ask each of them to introduce you personally to 5 PM companies they know
- Every PM in Winter Park / Grand County knows each other — one warm intro converts at 30–50%
- Target: 10 intros → 3–5 demos → 2–3 paying customers from this alone

**Tactic 2 — Direct host outreach on VRBO/Airbnb**
- Search VRBO and Airbnb: Winter Park, Steamboat Springs, Breckenridge, Vail, Telluride, Aspen
- Filter for hosts with 50+ reviews = experienced operators who feel the pain
- Send DM through the platform:
  > *"Hey [Name] — I noticed you have [X] reviews on VRBO. I built a 24/7 AI concierge that answers guest questions automatically so you're not fielding texts at midnight. Free for 30 days, no credit card. Want to try it?"*
- Do 20 DMs/day = 600/month → expect 5% reply rate → 30 replies → 10 demos → 5 sign-ups
- Personalize with their property name — makes a huge difference

**Tactic 3 — Colorado STR Facebook groups**
- Join and post in: "Winter Park Vacation Rental Owners," "Colorado Short Term Rental Owners," "Airbnb Hosts Colorado," "VRBO Owners Colorado"
- Post as a peer, not a salesperson:
  > *"I manage rentals in Winter Park and got tired of answering the same guest questions over and over, so I built an AI that handles it 24/7. Happy to give free access to a few hosts in exchange for honest feedback. DM me if interested."*
- Peer-to-peer tone converts 3–5x better than a sales pitch
- Target: 2–3 posts/week across groups → 20–30 DMs per post → 5–10 sign-ups per wave

---

### Phase 2 — Expand to Colorado (Months 3–6)
**Goal: 50 paying customers, $2K–$5K MRR**

**Tactic 4 — Google Business Profile + Local SEO**
- Register at Google Business: category = "Software Company," location = Winter Park, CO
- Get reviews from your first 10–20 customers (ask directly, make it easy — send them the link)
- Write 2 blog posts/month targeting long-tail keywords:
  - "AI concierge for vacation rental owners"
  - "Airbnb guest communication automation"
  - "VRBO auto-responder for hosts"
  - "vacation rental chatbot software"
- Takes 3–6 months to rank but compounds forever — free traffic with no ad spend

**Tactic 5 — Cold email PM companies**
- Build list: Guesty partner directory, VRMA member directory, Lodgify marketplace, Google "vacation rental management [city] CO"
- Target companies managing 20+ properties in CO ski towns
- Tools: Apollo.io (free up to 50 contacts/month), Instantly.ai for email warmup + sequencing
- Email template (3 lines max):
  > **Subject:** *Your guests have questions at 2am — this handles it*
  >
  > Hey [Name], I run a short-term rental AI concierge that answers guest questions 24/7 — WiFi passwords, check-in codes, local restaurants, all of it. Winter Park Management uses it and it's cut their guest call volume significantly.
  >
  > Worth a 15-minute demo? [Calendly link]
- Volume: 200 emails/week → 2–5% reply rate → 4–10 calls → 2–4 closings/week at scale

**Tactic 6 — Consultant affiliate program**
- Target: Escapia consultants, Guesty implementation partners, Lodgify setup specialists
- These are the people PM companies hire to set up their software stack — built-in trust
- Offer: 20% recurring commission (a $299/mo Agency plan = $59.80/mo per referral, every month)
- Reach out via LinkedIn: "I'd love to add a referral agreement — your clients need this anyway"
- One good consultant can refer 5–10 clients in their network

**Tactic 7 — VRMA (Vacation Rental Management Association)**
- Annual conference (usually October — check vrma.com)
- Options: rent a booth ($1,500–$3,000), attend as member ($300–$500), sponsor a breakout session
- Breakout session pitch: "How AI Is Cutting Guest Call Volume by 80% in Mountain Rentals"
- Meet 50+ PM company decision-makers in 3 days
- Bring printed one-pagers and a tablet with a live demo

---

### Phase 3 — National Launch (Months 6–12)
**Goal: 500 customers, $20K+ MRR**

**Tactic 8 — Google Paid Search**
- Keywords: "vacation rental AI chatbot," "Airbnb auto responder," "guest communication software," "short term rental AI"
- Budget to start: $500–$1,000/month
- Landing page must be single-purpose: one headline, one pain point, one CTA — no feature dump
- Expected metrics: $50–$100 cost per lead, $200–$400 cost per acquired customer
- Scale budget as CAC is proven → $5K/mo → $20K/mo

**Tactic 9 — YouTube + TikTok / Reels content**
- Core video: *"I built an AI that answers my Airbnb guests — here's what happened"*
- Show: real guest conversation (WiFi question, check-in question) → AI answers correctly → unanswered email notification → adding to KB in 10 seconds
- Short-form (60–90 sec): TikTok, Instagram Reels, YouTube Shorts — post 3x/week
- Long-form (10–15 min): full walkthrough tutorial on YouTube — post 1x/week
- One viral short = 500+ free signups. This is the highest-leverage marketing activity.

**Tactic 10 — Product Hunt Launch**
- Submit on a Tuesday morning (highest traffic day)
- Line up 50+ people from your network to upvote on launch day — first 4 hours matter most
- Aim for Top 5 of the day → 2,000–5,000 site visitors in 24 hours
- Have a limited-time deal ready: "3 months free for Product Hunt" or "Lifetime 30% off"
- Write a genuine maker story — why you built it, the real problem, real results

**Tactic 11 — Indiehackers + Reddit community posts**
- r/SideProject, r/startups, r/EntrepreneurRideAlong: post your build story with real numbers
- r/AirBnB, r/vrbo: post as a host sharing a tool you built — not as a marketer
- Indiehackers.com: post monthly revenue updates — the community loves transparency and cross-promotes
- Not a direct sales channel but builds credibility + earns backlinks + gets you on SaaS directories

**Tactic 12 — Customer referral program**
- Every customer gets a unique referral link
- Reward: 1 free month for every referral who pays for 3 months
- Track via Stripe with promo codes tied to referral IDs
- This turns your 50 happy customers into 50 salespeople — zero ad spend

---

## The Single Most Important Asset to Build

**A 60-second demo video.** Before any ads, before Product Hunt, before cold email — make this video.

Show:
1. Guest sends a message at midnight: "What's the WiFi password?"
2. AI responds instantly with the correct password
3. Guest asks something the AI doesn't know
4. Admin receives email notification with the question
5. Admin clicks "Add to Knowledge Base" — fills in the answer in 10 seconds
6. Done. Guest never knew there was a problem.

This video does more than any marketing campaign. Every sales email, every social post, every ad should point to it.

---

## Pricing Strategy (Final)

| Tier | Monthly | Annual (20% off) | Properties | Key Features |
|---|---|---|---|---|
| Solo Host | $40 | $384 ($32/mo) | Up to 3 | AI chat, email alerts, guest links |
| Pro | $99 | $950 ($79/mo) | Up to 20 | + team members, conversation history |
| Agency | $299 | $2,870 ($239/mo) | Up to 100 | + white-label, custom email, analytics |
| Enterprise | $499+ | Custom | Unlimited | + custom domain, dedicated support, API access |

---

## Quick-Win Checklist (Do this week)

- [ ] Record 60-second demo video — real guest conversation, real notification, real KB add
- [ ] Ask Rob and Eliot to each introduce you to 5 PM companies they know personally
- [ ] Send 20 VRBO/Airbnb DMs to hosts in CO ski towns (Winter Park, Steamboat, Breck first)
- [ ] Join 3 Colorado STR Facebook groups and post peer-to-peer message
- [ ] Register Google Business Profile for Condo Assistant
- [ ] Buy domain for marketing landing page (condo-assistant.com or similar)
- [ ] Draft one-page PDF one-pager for in-person conversations (VRMA, meetups, etc.)

---

*Last updated: May 6, 2026*

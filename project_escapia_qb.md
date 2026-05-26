---
name: Escapia → QuickBooks Integration
description: Pull reservation financials from Escapia CRM and log them daily into QuickBooks Online
type: project
---

## Goal
Automatically extract 6–7 financial values from each Escapia booking and push to QuickBooks Online daily.

## Values to extract
- Total reservation cost
- Cleaning fee
- Additional line item fees (pet fee, extra guest fee, etc.)
- Tax breakdowns (state, county, local, and any other entities — 3–4 lines)

## Stack
- **Escapia** — PMS/CRM (check if API access is enabled — may need to upgrade account)
- **QuickBooks Online** — accounting (confirmed)
- **Frequency** — daily sync

## Approach (in priority order)
1. **Escapia API** — cleanest. Pull reservation financials via API, push to QuickBooks API
2. **Scheduled CSV export** — Escapia exports financial report daily, script parses and pushes to QBO
3. **Web scraping** — last resort if no API access

## Action items for Monday 2026-05-04
- User to confirm if Escapia account has API access (contact Escapia support if unsure)
- Confirm QuickBooks Online (not Desktop) — confirmed
- Once API access confirmed, Orion will build the integration

## Why
Eliminate manual data entry of reservation financials into QuickBooks. Saves time, reduces errors, creates clean daily records per reservation.

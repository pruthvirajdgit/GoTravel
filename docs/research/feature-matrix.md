# GoTravel Competitor Feature Matrix

> Source: extracted from `docs/research/competitor-teardowns/README.md`.
> See the teardowns for citations and per-cell context.

**TripCase is dead** — Sabre discontinued it in 2023. We compare against
the live alternatives: TripIt, Wanderlog, Roadtrippers, Polarsteps,
Sygic Travel (renamed to Tripomatic), and Google Travel / Google Maps Lists
as the "default option" baseline.

## Feature × competitor matrix

| Feature                          | TripIt                           | Wanderlog                  | Roadtrippers           | Polarsteps               | Sygic / Tripomatic       | Google Travel/Maps        | **GoTravel (planned MVP)** |
|----------------------------------|----------------------------------|----------------------------|------------------------|--------------------------|--------------------------|---------------------------|------------------------------|
| Native iOS                       | ✅                               | ✅                         | ✅                     | ✅                       | ✅                       | ✅ (Maps)                | ✅                           |
| Native Android                   | ✅                               | ✅                         | ✅                     | ✅                       | ✅                       | ✅ (Maps)                | ✅                           |
| Web app                          | ✅                               | ✅                         | ✅                     | ✅                       | ✅                       | ✅                       | ❌ (not in MVP)             |
| watchOS                          | ✅                               | ❌                         | ❌                     | ❌                       | ❌                       | ❌                       | post-MVP                    |
| Email forwarding inbox           | ✅ universal `plans@tripit.com`  | ✅ per-trip address        | ❌                     | ❌                       | ❌                       | ❌                       | post-MVP                    |
| OAuth Gmail scanning             | ✅ free                          | ✅ Pro only                | ❌                     | ❌                       | ❌                       | ✅ native                 | post-MVP                    |
| OAuth Outlook scanning           | ✅                               | unconfirmed                | ❌                     | ❌                       | ❌                       | ❌                       | post-MVP                    |
| Link / URL paste                 | ❌                               | ⚠ partial                  | ❌                     | ❌                       | ❌                       | ⚠ Maps URL only          | ✅ tiered extractor (MVP)   |
| **Excel / CSV import**           | ❌                               | ❌                         | ❌                     | ❌                       | ❌                       | ❌                       | ✅ smart-capture (MVP)      |
| PDF / GPX / KML export           | ❌                               | ⚠ partial                  | ❌                     | ❌                       | ✅ all 3                 | ❌                       | post-MVP                    |
| Map-first view                   | ❌ list-first                    | ✅ split pane              | ✅ route-first         | ✅ world map             | ✅ split pane            | ✅ map-first              | ✅ map-first (MVP)          |
| Map provider                     | Google/Apple (handoff)           | Google Maps SDK            | Mapbox                 | unknown                  | OpenStreetMap            | Google Maps              | TBD post-research           |
| Day filtering on map             | ❌                               | ✅                         | ⚠ route-based          | ⚠ step-based            | ✅                       | ❌                       | ✅ (MVP)                    |
| Route line / directions          | ❌ handoff                       | ✅                         | ✅                     | ✅ GPS auto              | ✅                       | ✅                       | ✅ (MVP)                    |
| Offline maps                     | ❌                               | ✅ Pro                     | ✅ Premium             | ⚠ unconfirmed            | ✅ Premium               | ✅ free                   | post-MVP (offline data ✓)   |
| Multi-user real-time edit        | ❌                               | ✅                         | ✅ basic+              | ⚠ tracking-only          | ✅                       | ✅ (Lists)                | ✅ (MVP)                    |
| **Presence / live cursors**      | ❌                               | unconfirmed                | ❌                     | ✅ location share        | ❌                       | ❌                       | ✅ (MVP)                    |
| Trip sharing model               | View-only link                   | Edit / view link           | Invite link            | Public/followers/private | Edit / view link         | Shared list link         | Invite link + roles (MVP)   |
| Comments / @-mentions            | ❌                               | unconfirmed                | ❌                     | ✅                       | ❌                       | ❌                       | P1 (MVP)                    |
| Offline-first design             | ⚠ text only                      | ⚠ Pro only                 | ⚠ Premium only         | ⚠ GPS works              | ⚠ Premium only           | ✅ (Maps)                 | ✅ (MVP — not gated)        |
| Budget — per-item costs          | ❌                               | ✅                         | ❌                     | ❌                       | ✅                       | ❌                       | ✅ (MVP)                    |
| Budget — multi-currency          | ❌                               | ✅                         | ❌                     | ❌                       | ⚠ unconfirmed            | ✅ (flights/hotels)       | ✅ (MVP)                    |
| Expense splitting                | ❌                               | ⚠ partial                  | ❌                     | ❌                       | ⚠ per-traveler           | ❌                       | post-MVP                    |
| AI itinerary generation          | ❌                               | ✅ Pro                     | ✅ Autopilot           | ✅                       | ✅                       | ✅ Gemini                 | post-MVP                    |
| Booking / affiliate              | ❌                               | ✅ hotels                  | ✅ hotels              | ✅ travel books          | ✅ tours / tickets       | ✅ flights / hotels       | post-MVP                    |
| Email flight alerts              | ✅ Pro                           | ✅ Pro                     | ❌                     | ❌                       | ❌                       | ⚠ passive                 | post-MVP                    |
| Free-tier shape                  | Basic itinerary + 1 Gmail scan/mo| Most planning features     | 1 trip, 3 stops        | Most features            | No offline, limited AI   | All features              | Free MVP, paywall hooks     |
| Cheapest paid tier               | $49 / yr                         | ~$24–36 / yr (unconfirmed) | $35.99 / yr            | ~$3–5 / mo (unconfirmed) | $23.99 / yr              | Free forever              | TBD before launch           |
| Approx. user scale               | ~20M                             | ~1M+                       | 42M trips (not users)  | 20M+ travelers           | 3M+ travelers            | 2B+ Maps MAU              | —                            |
| iOS rating (★ / reviews)         | 4.8★ / 302K                      | 4.9★ / 19.6K               | 4.6–4.8★ / 61K         | 4.8★ / 370K              | 4.6★ / 3.3K              | n/a (Maps 4.9★)           | —                            |
| Most recent meaningful update    | Apr 2025                         | 2025                       | 2025 (Autopilot)       | 2025 (AI planner)        | May 2025 (v26.2)         | 2025 (Gemini)             | —                            |

## How to read this

- **Bold rows** are the categories where GoTravel uniquely combines features
  no incumbent does (Excel/CSV import + presence + offline-first-not-gated).
- **post-MVP** in the GoTravel column means it's planned for v2+, not that
  it's omitted forever.
- Cells marked `unconfirmed` are pending verification on physical-device
  installs during Phase 1 hands-on testing.

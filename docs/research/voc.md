# Voice of Customer — Themes from App Store, Play Store, and Reddit

> Source: Phase 1 research agent (`competitor-research`), May 2026. The
> per-competitor VoC details (review excerpts with star ratings and dates)
> are interleaved into `docs/research/competitor-teardowns/README.md`.
> This document is the **aggregated, cross-competitor view**.

## Top themes — most-mentioned **complaints** across the category

| #  | Theme                                                              | Most painful in                          | GoTravel response |
|----|--------------------------------------------------------------------|------------------------------------------|-------------------|
| 1  | Lack of real-time co-editing / "I can't plan with my partner live" | TripIt, Roadtrippers                     | MVP P0 — Stories 4.2 + 4.3 (presence + live edit) |
| 2  | Email parser fails on non-US / non-English booking formats         | TripIt                                   | Post-MVP — built into our universal-inbox roadmap |
| 3  | Offline access is gated behind a paid tier                         | Wanderlog (Pro), Roadtrippers (Premium), Tripomatic (Premium) | MVP — offline-first **not paywalled** |
| 4  | Free tier crippled post-acquisition ("bait and switch")            | Roadtrippers (60 → 6 → 3 stops)          | Publish "Free Forever" commitment pre-launch (`free-forever-commitment` todo) |
| 5  | Can't paste a booking URL and have it parsed                       | Every competitor                         | MVP P0 — Story 3.2 (link-paste smart extraction) |
| 6  | No spreadsheet / CSV import or export                              | Every competitor                         | MVP P0 — Story 3.1 + new `csv-export` todo |
| 7  | Map is map-second / list-first (poor visual planning)              | TripIt                                   | MVP — map-first home is the default screen |
| 8  | Need to use two apps: one to plan, one to track on-trip            | Polarsteps users vs Wanderlog users      | Post-MVP — `p8-planning-tracking-handoff` |
| 9  | No expense splitting (have to bounce to Splitwise / Venmo)         | Every competitor                         | Post-MVP — on the `p8-post-mvp` roadmap |
| 10 | App is US/Canada-only or has weak international POI data           | Roadtrippers (US/CA/NZ/AU only)          | MVP — global-first; Google Places fallback in SE Asia / Africa / LATAM where Mapbox POI data is thin |

## Top themes — most-mentioned **delights** across the category

| # | Theme                                                                  | Strongest in        | GoTravel response |
|---|------------------------------------------------------------------------|---------------------|-------------------|
| 1 | "I forwarded an email and a complete trip appeared in 30 seconds"      | TripIt              | Post-MVP — universal inbox |
| 2 | "Beautiful, frictionless route-on-a-map with day filtering"            | Wanderlog, Tripomatic | MVP P0          |
| 3 | "Trip recap / printable travel book as a keepsake"                     | Polarsteps          | Consider post-MVP — strong retention loop |
| 4 | "Apple Watch glance"                                                   | TripIt              | Post-MVP          |
| 5 | "Discover places nearby that other users loved"                        | Roadtrippers        | Post-MVP — public/shareable trip pages |
| 6 | "Auto-track my route via GPS — the trip records itself"                | Polarsteps          | Post-MVP — planning-to-tracking handoff |
| 7 | "Reorder today's stops to drive the least" (route optimization)        | Wanderlog Pro       | Consider for MVP P1 (Gap 6) |
| 8 | "Travel guidance for international requirements (visa, passport, vax)" | TripIt (2024–2025)  | Post-MVP          |

## Methodology + limits

- Sources: App Store reviews (US, UK, DE, JP storefronts where reachable),
  Play Store reviews, Reddit r/travel + r/solotravel + r/digitalnomad,
  Wanderlog's own (biased) head-to-head blog posts.
- Per-review quotes and star ratings are inline in the competitor teardowns.
- This summary is qualitative, not statistically weighted; the goal is to
  identify priority gaps, not to A/B test feature copy.
- **Validation gate before MVP launch:** run 5 unscripted user interviews
  against the top 5 complaint themes and confirm GoTravel's planned
  responses are credible to real users.

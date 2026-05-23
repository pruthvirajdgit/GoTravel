# GoTravel

A native mobile-first travel itinerary and trip planning application for **iOS and Android**.

> ⚠️ **Status:** Planning & research phase. No application code yet — this repository currently
> holds the product, research, and architecture documentation that will inform the build.

## Vision

The best travel-planning app on the market today still asks users to do too much typing,
juggle web tabs, and live inside list views that hide the geography of a trip. **GoTravel**
exists to fix three things at once:

1. **Map-first visualization** — the itinerary lives on a map, not buried in a list. Day
   filtering, route lines, and shared "live cursors" of co-planners are first-class.
2. **Multi-modal frictionless input** — paste a link, upload a (smart-captured) Excel/CSV
   spreadsheet, or add items manually with map-search autocomplete.
3. **Live multi-user collaboration** — low-latency presence, conflict-free synchronization,
   and offline edits that reconcile cleanly on reconnect.

Budget tracking with multi-currency support is woven through every item, not buried in a
separate tab.

## Differentiators vs. TripIt, Wanderlog, and Roadtrippers

> **Note:** TripCase (Sabre) was discontinued in 2023. We compare against
> live alternatives. See `docs/research/feature-matrix.md` for the full
> 7-competitor matrix.

| Capability                                | TripIt | Wanderlog | Roadtrippers | **GoTravel (planned MVP)** |
|-------------------------------------------|:------:|:---------:|:------------:|:--------------------------:|
| Native iOS + Android                      |   ✅   |    ✅     |     ✅       |            ✅              |
| Map-first itinerary view                  |   ❌   |    ✅     |     ✅       |            ✅              |
| Live multi-user editing **with presence** |   ❌   |  partial  |     ❌       |            ✅              |
| Link-paste smart import                   |   ❌   |  partial  |     ❌       |            ✅              |
| **Excel/CSV smart import**                |   ❌   |    ❌     |     ❌       |            ✅              |
| Multi-currency budget per item            | partial|    ✅     |     ❌       |            ✅              |
| Offline-first **not paywalled**           | text-only| Pro only| Premium only |            ✅              |

(Detailed teardowns: `docs/research/competitor-teardowns/`. Top-10 market
gaps: `docs/research/gap-analysis.md`.)

## Repository layout

```
docs/
  research/                # Phase 1 — competitor teardowns, VoC, tech landscape
    competitor-teardowns/  # One MD per competitor
  product/                 # Phase 2 — personas, user stories, data model, wireframes
    templates/             # Canonical Excel/CSV import templates
    wireframes/            # Low-fi wireframes (populated with the canonical fixture)
  architecture/            # Phase 3/4 — stack decision, system design, sync protocol
fixtures/                  # Canonical seed data (Budapest → Prague → Croatia trip)
```

## Planning document

The full implementation plan lives in the session workspace and is the single source of
truth for phasing and rationale. A condensed mirror lives in `docs/README.md`.

## License

TBD before public launch.

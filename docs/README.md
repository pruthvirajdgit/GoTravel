# GoTravel Documentation Index

This is the planning & research workspace for GoTravel. The full implementation plan
lives in the session workspace (`plan.md`); the documents under `docs/` are the
deliverables produced during each phase.

## Phases

| Phase | Topic                                  | Location                          |
|-------|----------------------------------------|-----------------------------------|
| 1     | Competitor teardowns                   | `research/competitor-teardowns/`  |
| 1     | Voice-of-customer themes               | `research/voc.md`                 |
| 1     | Feature matrix                         | `research/feature-matrix.md`      |
| 1     | Top-10 gap analysis                    | `research/gap-analysis.md`        |
| 1     | Tech landscape                         | `research/tech-landscape.md`      |
| 1     | Cross-platform framework recommendation| `research/framework-recommendation.md` |
| 2     | Personas                               | `product/personas.md`             |
| 2     | User stories                           | `product/user-stories.md`         |
| 2     | Data model                             | `product/data-model.md`           |
| 2     | Wireframes (populated with fixture)    | `product/wireframes/`             |
| 2     | Canonical Excel/CSV template           | `product/templates/`              |
| —     | Canonical seed dataset                 | `../fixtures/`                    |
| 3     | Stack decision                         | `architecture/stack-decision.md`  |
| 4     | System architecture                    | `architecture/system-diagram.md`  |
| 4     | Realtime sync protocol                 | `architecture/realtime-protocol.md` |
| 4     | Offline-first strategy                 | `architecture/offline-sync.md`    |
| 4     | Security & privacy                     | `architecture/security-and-privacy.md` |

## Canonical fixture

The **Budapest → Prague → Split → Hvar** itinerary (~10–14 days) is our canonical
sample dataset. Every wireframe, every Storybook story, and the in-app
"Load demo data" debug toggle are wired to it. The same data also ships as the
"example trip" inside the published Excel template. See `fixtures/` and
`product/templates/`.

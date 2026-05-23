# GoTravel User Journey — Screen Map

> The full sequence of screens the user touches from "I downloaded the
> app" → "I'm on my trip". Each screen has a corresponding SVG in
> `mockups/`. Use this as the order when wiring prototype links in Figma.

```
                                  ┌─────────────────────┐
                                  │ 01-splash           │
                                  └──────────┬──────────┘
                                             ▼
                                  ┌─────────────────────┐
                                  │ 02-signin           │
                                  └──────────┬──────────┘
                                             ▼
                                  ┌─────────────────────┐
                                  │ 03-home-empty       │
                                  └──────────┬──────────┘
                                             ▼
                                  ┌─────────────────────┐
                                  │ 04-create-trip      │
                                  │ (source picker)     │
                                  └──┬───┬────────────┬─┘
                                     │   │            │
              ┌──────────────────────┘   │            └────────────────┐
              ▼                          ▼                             ▼
   ┌─────────────────────┐   ┌─────────────────────┐       ┌─────────────────────┐
   │ 05a-excel-upload    │   │ 06-paste-link       │       │ (start blank)       │
   └──────────┬──────────┘   └──────────┬──────────┘       └──────────┬──────────┘
              ▼                          │                             │
   ┌─────────────────────┐               │                             │
   │ 05b-excel-column-   │               │                             │
   │ mapping (preview)   │               │                             │
   └──────────┬──────────┘               │                             │
              ▼                          │                             │
   ┌─────────────────────┐               │                             │
   │ 05c-excel-import-   │               │                             │
   │ success             │               │                             │
   └──────────┬──────────┘               │                             │
              └───────────┬──────────────┘─────────────────────────────┘
                          ▼
              ┌─────────────────────┐
              │ 07-home-populated   │  ← trip list with the new trip
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │ 08-trip-map-view    │  ← map-first home for the trip
              └─┬───┬─────────┬─────┘
                │   │         │
   ┌────────────┘   │         └────────────┐
   ▼                ▼                       ▼
┌─────────────┐  ┌─────────────────┐  ┌─────────────────────┐
│ 09-trip-day │  │ 13-share-       │  │ 12-budget-summary   │
│ view        │  │ collaborators   │  │                     │
└─────┬───────┘  └────────┬────────┘  └─────────────────────┘
      ▼                   ▼
┌─────────────┐  ┌─────────────────┐
│ 10-item-    │  │ 14-live-        │
│ detail      │  │ presence (over- │
│             │  │  laid on 08/09) │
└─┬───┬───────┘  └─────────────────┘
  │   │
  │   └──────────┐
  ▼              ▼
┌─────────┐  ┌─────────────────┐
│11-add-  │  │15-comments-     │
│item     │  │mentions         │
│search   │  │                 │
└─────────┘  └─────────────────┘

(Debug menu: 16-debug-menu is reachable from a long-press on
 the GoTravel logo on every screen; not part of the linear journey.)

Trip-day arrives → app auto-switches to trip mode:
              ┌─────────────────────┐
              │ 17-trip-mode-active │  ← POST-MVP
              └──────────┬──────────┘
                         ▼
              ┌─────────────────────┐
              │ 18-trip-recap       │  ← POST-MVP
              └─────────────────────┘
```

## Reading order for review

When you walk a reviewer through the prototype the first time, follow
this exact order — it tells the story end-to-end:

1. `01-splash` → `02-signin` → `03-home-empty`
2. `04-create-trip` (highlight the **three** import paths)
3. `05a-excel-upload` → `05b-excel-column-mapping` → `05c-excel-import-success`
   (this is the **canonical demo path** — deterministic, offline, fast)
4. `07-home-populated`
5. `08-trip-map-view` (this is the screen the entire app is built around)
6. `14-live-presence` (overlay variant of 08 — same screen, with three
   collaborator avatars and a live cursor)
7. `09-trip-day-view`
8. `10-item-detail`
8b. `10b-transit-detail` *(MVP — flight / train / bus / ferry / cab variant,
    shows terminal · gate / platform · seat · class · pickup details)*
9. `11-add-item-search`
10. `12-budget-summary`
11. `13-share-collaborators`
12. `15-comments-mentions`
13. `16-debug-menu` ("here's how we load the fixture in seconds during demos")
14. `17-trip-mode-active` *(post-MVP — say "future state")*
15. `18-trip-recap` *(post-MVP)*

## Notes for the Figma rebuild

- Keep `01–16` in a `MVP` page; `17–18` in a `POST-MVP` page.
- Each screen is **375 × 812** (iPhone 13 / 14 / 15 frame).
- The map background in 08 / 09 / 14 / 17 is a stylized mock — replace
  with a real Mapbox Static Image API render at handoff.
- All copy is final unless marked `[draft]`.
- All sample data comes from `fixtures/sample-trip-budapest-prague-
  croatia.json` (Maya, Anna, James, Leo; 13 days; 41 items).

# GoTravel Wireframes — Textual / ASCII (Draft v0.1)

> Status: **Draft** — textual wireframes are the v0 deliverable. Hi-fi Figma
> mockups will be produced during Phase 6 once the framework is picked and
> we have the component library standing up.
>
> **Every wireframe is populated with the canonical fixture data**
> (`fixtures/sample-trip-budapest-prague-croatia.json`). This enforces the
> "fixtures-everywhere" principle: no screen is ever designed against an
> empty state alone.

## Screen index

1. [Trip list (home)](#1-trip-list-home)
2. [Trip home — map-first with bottom-sheet itinerary](#2-trip-home--map-first-with-bottom-sheet-itinerary)
3. [Day view (timeline)](#3-day-view-timeline)
4. [Item detail / edit](#4-item-detail--edit)
5. [Excel/CSV upload — file picker → column-mapping preview → commit](#5-excelcsv-upload-flow)
6. [Paste-a-link flow with extraction preview](#6-paste-a-link-flow-with-extraction-preview)
7. [Collaborator invite + presence](#7-collaborator-invite--presence)
8. [Budget summary](#8-budget-summary)
9. [Debug menu — Load demo data](#9-debug-menu--load-demo-data)

Conventions: `[...]` = tappable. `<...>` = input. `⌘` = system action.
Persona-of-the-user for the mockups: **Maya** (Group Trip Organizer, primary).

---

## 1. Trip list (home)

```
┌─────────────────────────────────────────────────────────┐
│  GoTravel                              [⚙]   [+ New]    │
├─────────────────────────────────────────────────────────┤
│  My trips                                                │
│                                                          │
│  ┌──────────────────────────────────────────────────┐   │
│  │  ╔══════════════════════════════════════════════╗│   │
│  │  ║       cover image (Hvar fortress)            ║│   │
│  │  ╚══════════════════════════════════════════════╝│   │
│  │  Central Europe + Adriatic              ●●●●     │   │
│  │  Jun 13 – Jun 25  ·  4 collaborators  · 13 days │   │
│  │  [resume planning]                                │   │
│  └──────────────────────────────────────────────────┘   │
│                                                          │
│  ┌──────────────────────────────────────────────────┐   │
│  │  + Create a trip                                  │   │
│  └──────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  ⇪ Import from spreadsheet                        │   │
│  └──────────────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────────────┐   │
│  │  ⇲ Paste a link                                   │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

Notes:
- Trip card shows up to 4 collaborator avatars (●●●●).
- Avatar with a green ring = currently active in the trip (presence).
- Bottom three actions correspond to the three input methods.

---

## 2. Trip home — map-first with bottom-sheet itinerary

The hero screen. Map dominates; itinerary is a draggable bottom sheet.

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Central Europe + Adriatic    Maya·Anna·James·Leo ●   │
│  ┌─────────────────────────────────────────────────┐    │
│  │  [All days ▾]                          [📍] [⚙] │    │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│             ┌───────────────────────────┐                │
│        🇭🇺 │  Budapest                  │                │
│            │   📍📍📍📍                  │                │
│            │       \                    │                │
│            │        🚆 (EC 273)         │                │
│            │         \                  │                │
│        🇨🇿 │    Prague                  │                │
│            │     📍📍📍📍📍              │                │
│            │       \                    │                │
│            │        ✈️ (QS 1131)        │                │
│            │         \                  │                │
│        🇭🇷 │     Split 📍📍📍           │                │
│            │            \  ⛴ Krilo      │                │
│            │             Hvar 📍📍📍    │                │
│            └───────────────────────────┘                │
│  ╭──────────────────────────────────────────────────╮   │
│  │  ━━━  (drag handle)                              │   │
│  │  Day 5  ·  Wed, Jun 17  ·  Train to Prague       │   │
│  │  ──────────────────────────────────────────────  │   │
│  │  07:25  🚆 EuroCity EC 273 'Metropolitan'        │   │
│  │         BUD Keleti → Praha hl.n.       BOOKED    │   │
│  │  ──────────────────────────────────────────────  │   │
│  │  15:30  🛏 Hotel U Prince (3 nights)             │   │
│  │         Staroměstské nám. 29           BOOKED    │   │
│  │  ──────────────────────────────────────────────  │   │
│  │  20:00  🍽 Lokál Dlouhááá                         │   │
│  │         Dlouhá 33, Staré Město       PLANNED    │   │
│  │                                                  │   │
│  │  [+ Add item]                                    │   │
│  ╰──────────────────────────────────────────────────╯   │
└─────────────────────────────────────────────────────────┘
```

Notes:
- Header avatars: presence ring (green = active in this trip right now).
  Maya and James both have green rings (matches fixture's `presence[]`).
- Day selector `[All days ▾]` filters pins on the map.
- Transit pins draw connecting polylines in the country color.
- Bottom sheet is draggable: collapsed shows the active day; expanded shows
  all days in scrollable accordion.
- Tapping a pin scrolls the bottom sheet to that item (and v.v.).

---

## 3. Day view (timeline)

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Day 6 of 13       Thu, Jun 18 — Old Town & Castle   │
├─────────────────────────────────────────────────────────┤
│  [< Day 5]                                     [Day 7 >]│
│                                                          │
│   09:00 ──┐ 🏛  Old Town Square & Astronomical Clock   │
│           │     ⏱ 1h · 📍 Staroměstské nám.            │
│           │     PLANNED                                  │
│           │                                              │
│   10:30 ──┤ 🚶 Walk Charles Bridge → Malá Strana       │
│           │     ⏱ 1h 30m · 📍 Karlův most              │
│           │     PLANNED                                  │
│           │                                              │
│   14:00 ──┤ 🏰 Prague Castle complex                   │
│           │     ⏱ 3h · 📍 Hradčany                      │
│           │     BOOKED · ref HRADCZ-2026-06-18-14      │
│           │     1,800 CZK / person                       │
│           │                                              │
│   20:30 ──┤ 🍽 Dinner at U Medvídků (own brewery)     │
│           │     ⏱ 1h 45m · 📍 Na Perštýně 7            │
│           │     PLANNED                                  │
│           │     2,800 CZK total (shared)                 │
│                                                          │
│  ┌────────────────────────────────────────────────────┐ │
│  │ + Add item                                         │ │
│  └────────────────────────────────────────────────────┘ │
│                                                          │
│  Day total: 4,640 CZK  ≈  283 CAD                       │
└─────────────────────────────────────────────────────────┘
```

Notes:
- Drag-handles on the left rail allow reorder.
- Status pill ('PLANNED', 'BOOKED', 'COMPLETED') is colored.
- Day total at the bottom in trip's home currency.

---

## 4. Item detail / edit

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Prague Castle complex                       [⋯]      │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────┐    │
│  │                                                 │    │
│  │      📷 (attached: castle-courtyard.jpg)        │    │
│  │                                                 │    │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  Title       <Prague Castle complex (timed entry 14:00)>│
│  Type        [Activity ▾]                                │
│  Status      [Booked ▾]                                  │
│  When        Thu, Jun 18  14:00 – 17:00                  │
│  Where       📍 Hradčany, 119 08 Praha 1                 │
│              (tap to view on map)                        │
│  Booking     ref: HRADCZ-2026-06-18-14                   │
│              [↗ https://www.hrad.cz/]                    │
│  Cost        <1,800> [CZK ▾]    [Personal ▾]            │
│  Category    [Sightseeing ▾]                             │
│  Notes       ┌────────────────────────────────────────┐  │
│              │ Circuit B covers St. Vitus, Old Royal  │  │
│              │ Palace, Basilica, Golden Lane.         │  │
│              └────────────────────────────────────────┘  │
│  Attachments [+] castle-courtyard.jpg                    │
│                                                          │
│  ▸ Comments (1)                                          │
│    James  · 2 days ago                                   │
│    "Should we pre-book the tower climb too?"             │
│    [Reply]                                               │
│                                                          │
│  ─────────────────────────────────────────────           │
│  Activity                                                │
│  Created by Maya · Feb 24                                │
│  Last edited by Maya · Feb 24                            │
└─────────────────────────────────────────────────────────┘
```

Notes:
- All form fields are inline-editable on tap.
- Notes field uses CRDT text — see realtime spec.
- The presence indicator at the top right shows other users currently
  viewing this item.

---

## 5. Excel/CSV upload flow

### 5a. Empty state of the file picker entry point

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Import from spreadsheet                              │
├─────────────────────────────────────────────────────────┤
│                                                          │
│        ⇪                                                 │
│        Drop or pick an Excel or CSV file                │
│                                                          │
│        We support .xlsx and .csv. Use our template      │
│        for the smoothest experience.                    │
│                                                          │
│        [⇣ Download template]                            │
│        [⇪ Pick a file]                                  │
│                                                          │
│        ─────  or  ─────                                 │
│                                                          │
│        [⇲ Try the demo spreadsheet]                     │
│        (Budapest → Prague → Croatia, 13 days, 41 items) │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### 5b. Column-mapping preview (after picking the example trip)

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Preview import — gotravel-example-trip.csv           │
│                                       41 items detected │
├─────────────────────────────────────────────────────────┤
│  We detected the following structure:                    │
│                                                          │
│  ✅ day, date, start_time, end_time, type, title         │
│  ✅ city, location_name, address, lat, lng              │
│  ✅ status, booking_ref, booking_url                    │
│  ✅ cost_amount, cost_currency, cost_split              │
│  ✅ notes, source_url                                   │
│  ✅ transit_mode, carrier, vehicle_no, from_location,   │
│     to_location                                         │
│  ✅ check_in, check_out, nights, room_type, guests      │
│  ✅ meal_type, cuisine, category, ticket_required       │
│                                                          │
│  All columns mapped exactly. No fix-ups needed.         │
│                                                          │
│  Trip name      <Central Europe + Adriatic>             │
│  Home currency  [CAD ▾]                                  │
│  Dates          Jun 13 → Jun 25 (auto-detected)         │
│                                                          │
│  ─────────────────────────────────────────────          │
│  Sample of first 3 items:                                │
│                                                          │
│  Day 1 · 18:25  ✈ Flight YYZ → BUD (AC1810)             │
│                  1,245.00 CAD · Booked                   │
│  Day 1 · 15:00  🛏 Hotel Rum Budapest (4 nights)        │
│                  144,000 HUF · Booked                    │
│  Day 1 · 19:30  🍽 Belvárosi Disznótoros                 │
│                  16,000 HUF · Planned                    │
│  [show all 41]                                          │
│                                                          │
│  ┌──────────────────────────────────────────────────┐   │
│  │  [Cancel]                  [Create trip from CSV]│   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

### 5c. Column-mapping preview (when columns DON'T match)

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Preview import — my-amalfi-trip.xlsx                 │
│                                       18 items detected │
├─────────────────────────────────────────────────────────┤
│  We did our best to map your columns:                    │
│                                                          │
│  Your column        →  GoTravel field           Confidence│
│  "Day #"            →  day                       ✅ 100%  │
│  "Date"             →  date                      ✅ 100%  │
│  "What"             →  title                     ⚠️ 78%   │
│                        [change to ▾]                     │
│  "Where"            →  location_name             ⚠️ 75%   │
│                        [change to ▾]                     │
│  "Cost ($)"         →  cost_amount               ✅ 92%   │
│                        Currency: assumed USD             │
│                        [change to ▾]                     │
│  "Notes"            →  notes                     ✅ 100%  │
│  "Booked?"          →  status                    ⚠️ 65%   │
│                        true → booked, false → planned   │
│                        [change to ▾]                     │
│  "Confirmation #"   →  booking_ref               ✅ 90%   │
│                                                          │
│  3 rows need attention:                                  │
│  ⚠ Row 7: type couldn't be inferred                     │
│    [set type to ▾]                                       │
│  ⚠ Row 12: address didn't geocode                        │
│    [enter manually]                                      │
│                                                          │
│  ┌──────────────────────────────────────────────────┐   │
│  │  [Cancel]   [Fix all]   [Create trip from sheet] │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

## 6. Paste-a-link flow with extraction preview

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Paste a link                                         │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────┐    │
│  │ https://www.hotelrumbudapest.com/               │    │
│  └─────────────────────────────────────────────────┘    │
│  [Extract]                                               │
│                                                          │
│  ━━━  Extracting…  ━━━                                  │
│  Tier 1: Open Graph ✅ (180 ms)                         │
│  Tier 2: JSON-LD     ✅ (60 ms)                          │
│  Geocoding…          ✅ (220 ms)                         │
│                                                          │
│  Detected as:                                            │
│  ┌─────────────────────────────────────────────────┐    │
│  │  🛏  Hotel Rum Budapest                          │    │
│  │     📍 Királyi Pál u. 4, 1053 Budapest          │    │
│  │     ★★★★ · boutique hotel                       │    │
│  │                                                 │    │
│  │     [📷 og:image preview]                       │    │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  Add to        [Central Europe + Adriatic ▾]            │
│  Day           [Day 1 — Jun 13 ▾]                       │
│  Type          [Lodging ▾]                               │
│  Title         <Hotel Rum Budapest>                     │
│  Check-in      <2026-06-13> <15:00>                     │
│  Nights        <4>                                       │
│                                                          │
│  ┌──────────────────────────────────────────────────┐   │
│  │  [Cancel]                              [Add item]│   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

When extraction is uncertain (e.g. TikTok URL), the preview shows lower
confidence and asks the user to confirm the type and location.

---

## 7. Collaborator invite + presence

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Trip members                                         │
├─────────────────────────────────────────────────────────┤
│  Active right now (2)                                    │
│  ┌─────────────────────────────────────────────────┐    │
│  │ ●  Maya Chen (you)                               │    │
│  │    Looking at Day 7 — Pick a dinner spot         │    │
│  │ ●  James O'Connell                               │    │
│  │    Editing Day 7 — Pick a dinner spot · cursor 42│   │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  All members (4)                                         │
│  ─────────────────────────────────────────────           │
│  Maya Chen           Owner       since Nov 2            │
│  Anna Park           Editor      invited by you, Nov 3  │
│  James O'Connell     Editor      invited by you, Nov 3  │
│  Leo Müller          Commenter   invited by Anna, Nov 4 │
│                                                          │
│  Invite link                                             │
│  ┌─────────────────────────────────────────────────┐    │
│  │ https://gotravel.app/i/abcd1234                  │    │
│  └─────────────────────────────────────────────────┘    │
│  Role for new joiners  [Editor ▾]                       │
│  Expires after         [7 days ▾]                       │
│  [⌥ Copy link]   [↻ Regenerate]   [✕ Disable link]      │
└─────────────────────────────────────────────────────────┘
```

---

## 8. Budget summary

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Budget — Central Europe + Adriatic                   │
├─────────────────────────────────────────────────────────┤
│  Showing in CAD  (Maya's home currency)        [⚙]      │
│                                                          │
│  Trip total                                              │
│  ┌─────────────────────────────────────────────────┐    │
│  │  CAD 6,840  (≈)                                  │   │
│  │  Booked: 4,510  · Planned: 1,920 · Other: 410   │   │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  By currency                                             │
│  ┌─────────────────────────────────────────────────┐    │
│  │  CAD 1,245   (1 item)                            │   │
│  │  HUF 1,896,000 ≈ CAD 730  (15 items)             │   │
│  │  CZK 27,310 ≈ CAD 1,610  (10 items)              │   │
│  │  EUR 1,930 ≈ CAD 2,840  (14 items)               │   │
│  │  Other: shared kitty 40 EUR ≈ 58 CAD             │   │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  By day                                                  │
│  Day 1   1,615 CAD  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓     │
│  Day 2     115 CAD  ▓▓                                  │
│  Day 3      80 CAD  ▓                                   │
│  ...                                                     │
│  Day 9   1,090 CAD  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓                │
│  Day 11  1,055 CAD  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓                 │
│                                                          │
│  FX rates as of 2026-05-23 · refreshed daily            │
└─────────────────────────────────────────────────────────┘
```

Notes:
- Costs roll up from items; multi-currency is collapsed to home currency.
- "Other" is the trip-level `costs[]` (shared kitty).
- A per-person view (rough split) is reachable from `[⚙]` — full expense
  splitting is post-MVP.

---

## 9. Debug menu — Load demo data

(Only present in dev/debug builds.)

```
┌─────────────────────────────────────────────────────────┐
│  ◂ Debug                                                 │
├─────────────────────────────────────────────────────────┤
│  Fixtures                                                │
│  ┌─────────────────────────────────────────────────┐    │
│  │  [⇲ Load Budapest → Prague → Croatia (13 days)] │   │
│  │  Creates a fresh trip in your account            │   │
│  │  hydrated from the canonical fixture.            │   │
│  │  Includes 4 collaborators, 41 items, 5 comments. │   │
│  │  Bumps version: fixtureVersion 0.1.0             │   │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  Sync                                                    │
│  [Toggle offline mode]                                   │
│  [Clear local cache]                                     │
│  [Force resync]                                          │
│                                                          │
│  Storybook                                               │
│  [Open component library]                                │
│                                                          │
│  Flags                                                   │
│  ☐ Show realtime debug overlay                          │
│  ☐ Verbose link-extraction logs                          │
└─────────────────────────────────────────────────────────┘
```

---

## Open questions for hi-fi

- Should the bottom sheet on the trip home screen be three-state
  (collapsed / half / full) or two-state? Competitor research will help.
- Map theme — light only, or follow system dark mode? (Mapbox/Google both
  support both.)
- Do we show a "you might also like" affordance on the map when a day has
  fewer than N items? Could be a strong post-MVP differentiator.

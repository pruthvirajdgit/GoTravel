# GoTravel — Design Mockups

> **18 screens covering the complete user journey** — from first launch through trip planning, real-time collaboration, day-of travel, and post-trip recap.
>
> All mockups are **Figma-import-ready SVGs** at native iPhone 13/14/15 logical pixels (375 × 812). Drop any file into Figma and it becomes a fully-editable Frame with selectable layers.

---

## What's in here

```
design/
├── README.md                 ← you are here · handoff + Figma import guide
├── design-system.md          ← source of truth: colors, type, spacing, components
├── journey.md                ← screen flow map + reading order for review walkthrough
├── index.html                ← open in any browser → clickable prototype board
└── mockups/                  ← 18 × .svg, 375 × 812
    ├── 01-splash.svg
    ├── 02-signin.svg
    ├── 03-home-empty.svg
    ├── 04-create-trip.svg
    ├── 05a-excel-upload.svg          ← canonical DEMO path
    ├── 05b-excel-column-mapping.svg  ← canonical DEMO path
    ├── 05c-excel-import-success.svg  ← canonical DEMO path
    ├── 06-paste-link.svg
    ├── 07-home-populated.svg
    ├── 08-trip-map-view.svg          ← HERO screen
    ├── 09-trip-day-view.svg
    ├── 10-item-detail.svg
    ├── 11-add-item-search.svg
    ├── 12-budget-summary.svg
    ├── 13-share-collaborators.svg
    ├── 14-live-presence.svg          ← HERO screen (collab differentiator)
    ├── 15-comments-mentions.svg
    ├── 16-debug-menu.svg              ← internal builds only
    ├── 17-trip-mode-active.svg        ← POST-MVP
    └── 18-trip-recap.svg              ← POST-MVP
```

---

## Quickstart

### 1. See everything at once (no Figma needed)
Open `design/index.html` in any modern browser. You'll get a phone-framed gallery of all 18 screens grouped by user-journey stage, with quick-jump nav and click-to-open-fullsize.

### 2. Walk the journey
Read `journey.md` for the screen-to-screen flow, then walk the **Reading order for review** sequence at the bottom of that file — it's the demo script.

### 3. Import into Figma
- **Single screen:** drag an `.svg` from `design/mockups/` directly into a Figma canvas. It will land as a top-level Frame named after the file's `<title>` element.
- **All 18:** select all 18 SVGs in your OS file picker → drag the whole selection into Figma. Each becomes its own Frame; arrange them on a board.
- Inside each Frame, every shape, text node, and group is a real selectable Figma layer.

### 4. Rebind hex literals to Figma Variables (optional but recommended)
The SVGs use literal hex codes everywhere (e.g. `#0E7C7B` for primary teal). To swap to Variables:

1. Create a Figma Variables collection named **GoTravel/Color** with the tokens listed in [`design-system.md`](./design-system.md) (color tokens table).
2. Open a Frame, select any object using primary teal, look up its fill in the right-panel → click the **○** dot beside the color → assign to `GoTravel/Color/primary`.
3. Repeat for the other tokens. The plugin **"Variables From Styles"** can speed this up. Or run a one-shot **"Find & Replace Color"** plugin (e.g. Color Swap) using the mapping in `design-system.md`.

Same approach works for type tokens — the SVGs use literal `font-size` values that map 1:1 to the type scale in `design-system.md`.

---

## What's MVP vs POST-MVP

| Stage | Screens | Status |
|---|---|---|
| Onboarding | 01, 02, 03 | **MVP** |
| Create trip | 04, 05a–c, 06 | **MVP** (05a–c is the demo path) |
| Plan & explore | 07, 08, 09, 10, 11, 12 | **MVP** |
| Collaborate | 13, 14, 15 | **MVP** |
| Internal tools | 16 | **MVP** (debug-only build) |
| On the road | 17 | **POST-MVP** (banner-flagged on the SVG) |
| Post-trip | 18 | **POST-MVP** (banner-flagged on the SVG) |

Post-MVP screens carry an orange `POST-MVP` pill in the top-right of the artboard so reviewers always know what's locked-in vs aspirational.

---

## Design tokens & component library

Every visual decision (color, type, spacing, radius, shadow, component anatomy) lives in [`design-system.md`](./design-system.md). When in doubt, that file is the source of truth — these SVGs render it, not the other way around.

Highlights:
- **Brand teal** `#0E7C7B`, **action coral** `#FF7A59`, **success green** `#2DA771`.
- **Five-color collaborator rotation** for avatars (Maya `#4F46E5`, Anna `#0EA5E9`, James `#10B981`, Leo `#F59E0B`, +1 spare).
- Type scale anchored on Inter (system font fallback). Eight steps from 11/700 caps → 40/700 display.
- 4-px spacing grid · 10/12/14/16/20/24 px radii · single sheet shadow.

---

## Canonical fixture (used in every mockup)

Every screen is populated with real-looking data from **`fixtures/sample-trip-budapest-prague-croatia.json`** at the repo root:

- **Route:** Budapest → Prague → Split → Hvar
- **Dates:** Jun 14 – 26 2026 (13 days)
- **Volume:** 41 items, 38 locations, multi-currency (HUF · CZK · EUR)
- **Members:** Maya (owner) · Anna (editor) · James (editor) · Leo (commenter)

This is also the dataset that loads via the debug menu (screen 16) and the example trip shipped inside the Excel template (`docs/product/templates/gotravel-example-trip.csv`). One source of truth, three surfaces.

---

## Why SVGs and not a native `.fig` file?

`.fig` is a closed binary format. There is no public spec, no SDK that writes it, and no Figma feature that ingests anything other than `.fig`, `.svg`, `.png`, and a handful of plugin-imported formats. The standard workflow for AI-generated UI is therefore:

1. Generate **lossless, layered SVGs** at the target frame size.
2. Drag into Figma → each becomes an editable Frame with full layer fidelity.
3. (Optional) Rebind raw colors / text styles to Variables and Styles.

That's what you have here. No fidelity is lost — anything in a `.fig` file can be reconstructed by editing these Frames inside Figma.

---

## Related docs

- [`design-system.md`](./design-system.md) — visual tokens · component anatomy
- [`journey.md`](./journey.md) — screen flow + review walkthrough order
- [`../docs/research/voc.md`](../docs/research/voc.md) — user research that drove the IA
- [`../docs/architecture/realtime-protocol.md`](../docs/architecture/realtime-protocol.md) — what the collab screens (14, 15) actually do under the hood
- [`../fixtures/sample-trip-budapest-prague-croatia.json`](../fixtures/sample-trip-budapest-prague-croatia.json) — the data you see in every mockup

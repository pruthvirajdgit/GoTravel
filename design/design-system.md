# GoTravel Design System (v1.0)

> Source of truth for the visual language of every screen. Mirror these
> tokens into Figma Variables (Local Variables panel → import JSON
> matching the values below). All SVG mockups in this directory use these
> exact tokens — if you change a token here, propagate it.

## Brand

- **Name:** GoTravel
- **Voice:** confident, calm, group-friendly. Never shouty. Plain
  English; no startup jargon.
- **Logotype:** "GoTravel" in semibold sans, with a small map-pin glyph
  (◉) as the dotless `o` mark. Color: `--color-primary`.

## Color tokens

| Token                  | Hex       | Use                                                       |
|------------------------|-----------|-----------------------------------------------------------|
| `--color-primary`      | `#0E7C7B` | Brand. Primary CTAs, active tabs, link color, brand logo. |
| `--color-primary-fg`   | `#FFFFFF` | Text on primary surfaces.                                 |
| `--color-accent`       | `#FF7A59` | Highlights, "today" marker, presence pulse.               |
| `--color-bg`           | `#F8FAFB` | App background.                                           |
| `--color-surface`      | `#FFFFFF` | Cards, sheets, navigation surfaces.                       |
| `--color-surface-alt`  | `#EDF2F5` | Subtle panels, search backgrounds.                        |
| `--color-text`         | `#0F1F2E` | Primary text.                                             |
| `--color-text-muted`   | `#5A6B7A` | Secondary text, captions, helper copy.                    |
| `--color-text-subtle`  | `#92A1AE` | Placeholder / metadata.                                   |
| `--color-border`       | `#E5EBF0` | Dividers, card borders.                                   |
| `--color-success`      | `#2DA771` | Confirmed bookings, sync OK, "you're online".             |
| `--color-warning`      | `#F2A93B` | Sync degraded, missing field warning.                     |
| `--color-danger`       | `#E5484D` | Errors, delete, destructive.                              |
| `--color-info`         | `#3E7BFA` | Info banners.                                             |
| **Per-collaborator (presence)** | | Five-color rotation for collaborator avatars/cursors.  |
| `--color-collab-1`     | `#4F46E5` | Maya (owner — fixture)                                    |
| `--color-collab-2`     | `#0EA5E9` | Anna (editor — fixture)                                   |
| `--color-collab-3`     | `#10B981` | James (editor — fixture)                                  |
| `--color-collab-4`     | `#F59E0B` | Leo (commenter — fixture)                                 |
| `--color-collab-5`     | `#EC4899` | 5th member fallback                                       |

### Item-type accent colors (for map pins and day-view icons)

| Item type        | Color     | Icon (Material/SF symbol concept) |
|------------------|-----------|-----------------------------------|
| `flight`         | `#3E7BFA` | airplane                          |
| `train`          | `#7C5CE6` | tram                              |
| `transit`        | `#7C5CE6` | bus                               |
| `hotel`          | `#0E7C7B` | bed                               |
| `restaurant`     | `#FF7A59` | utensils                          |
| `activity`       | `#2DA771` | star                              |
| `landmark`       | `#F2A93B` | landmark                          |
| `note`           | `#92A1AE` | note                              |

## Typography

System fonts only (SF Pro on iOS, Roboto on Android) — Inter as the
Figma proxy. **Size / line-height / weight**:

| Style       | Size | LH  | Weight | Use                                  |
|-------------|-----:|----:|-------:|--------------------------------------|
| `display`   |  34  | 40  | 700    | Splash hero, empty-state hero        |
| `h1`        |  28  | 34  | 700    | Screen titles                        |
| `h2`        |  22  | 28  | 600    | Section headers                      |
| `h3`        |  17  | 22  | 600    | Card titles, item titles             |
| `body`      |  15  | 22  | 400    | Body text                            |
| `body-strong`|  15 | 22  | 600    | Emphasized inline                    |
| `caption`   |  13  | 18  | 400    | Metadata, helper text                |
| `caption-strong` | 13 | 18 | 600   | Pills, labels                        |
| `mono`      |  13  | 18  | 500    | Codes, amounts in dense tables       |

## Spacing scale (4-pt grid)

`4 · 8 · 12 · 16 · 24 · 32 · 48 · 64`

Touch targets: minimum **44 × 44 pt**. Buttons: **48** tall by default,
**56** for primary CTAs on signup/onboarding.

## Radius

- Buttons / chips: `12`
- Cards / sheets: `16`
- Bottom sheets: `24` top corners only
- Avatars: full circle
- Map pins: pin shape (28 × 36 with 10-radius head)

## Shadow

- `--shadow-card`: `0 2 8 rgba(15,31,46,0.06)`
- `--shadow-elev`: `0 8 24 rgba(15,31,46,0.10)` — for sheets, modals,
  floating action button.

## Device frame (used in every mockup)

- Canvas: **375 × 812** (iPhone 13 / 14 / 15 logical px).
- Status bar height: **44** (time + signal/wifi/battery).
- Bottom safe area: **34** (home indicator).
- Top safe area for content (excluding status bar): starts at **y=44**.
- Bottom nav: **49 + 34 safe = 83** tall.
- Content area (with bottom nav): **y=44 → y=729**, height **685**.
- Content area (no bottom nav): **y=44 → y=778**, height **734**.

## Component reference

| Component         | Shape                                                                              |
|-------------------|------------------------------------------------------------------------------------|
| Primary button    | 48h, radius 12, `--color-primary` bg, white text, `h3` weight.                     |
| Secondary button  | 48h, radius 12, surface bg, 1px `--color-border`, `--color-text` text.             |
| Ghost button      | 48h, radius 12, transparent, `--color-primary` text, no border.                    |
| Text input        | 48h, radius 12, `--color-surface-alt` bg, 1px transparent → primary on focus.      |
| Card              | radius 16, surface bg, `--shadow-card`, 16 padding.                                |
| Pill / tag        | 24h, radius 12, 8 horizontal padding, `caption-strong`.                            |
| Map pin (item)    | 28w × 36h pin shape, head colored by item-type, 14×14 white inner circle, item icon. |
| Day chip          | 64w × 56h, radius 12, "Day N" + date stacked, active = `--color-primary`.          |
| Avatar (40)       | 40 circle, 2px white border, initial in white, color from `--color-collab-N`.      |
| Avatar (24, presence) | 24 circle, 2px white border, pulse halo when actively editing.                 |

## Empty-state pattern

Centered: 96-radius circular soft-color background, illustration glyph
(map / spreadsheet / link), `h1` heading, `body` muted helper text,
primary CTA, optional ghost-button secondary action.

## Accessibility

- Minimum text contrast 4.5:1 against backgrounds (verified against
  `--color-text` on `--color-bg` and on surface).
- Tappable element: 44 × 44 minimum.
- Reduce-motion respected on the presence pulse and bottom-sheet
  transitions.
- All icons paired with text labels or have `aria-label` set.

## Figma import workflow

1. Open Figma → new file → drag any SVG from `design/mockups/` onto the
   canvas. Figma converts it to a **Frame** with each top-level group as
   a named layer.
2. Manually create local Figma Variables that match the tokens in this
   file (color, type, spacing). The SVG layer fills resolve to literal
   hex; rebind them to the variables after import.
3. Wire screens together with prototype links following the order in
   `journey.md`.

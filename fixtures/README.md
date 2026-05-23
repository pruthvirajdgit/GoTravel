# Canonical Fixture Data

This folder holds the **canonical seed data** for GoTravel UI development,
Storybook stories, and the in-app "Load demo data" debug toggle.

## Files

| File                                            | Purpose                                |
|-------------------------------------------------|----------------------------------------|
| `sample-trip-budapest-prague-croatia.json`      | Full Budapest → Prague → Split → Hvar trip as a JSON object matching the data model (`docs/product/data-model.md`). The single source of truth for the canonical trip's data. |

## Why this exists

Per the GoTravel "fixtures-everywhere" principle, **no screen is designed or
built against an empty state alone**. Every editable field, list, map, and
component must have meaningful seed data available during development.

The canonical fixture exercises:

- **3+ cities** (Budapest, Szentendre day-trip, Prague, Kutná Hora day-trip,
  Split, Hvar — 13 days, 6 city stops).
- **Intercity transit**: train Budapest → Prague (EuroCity), flight
  Prague → Split (Smartwings), catamaran Split → Hvar (Krilo).
- **Lodging in every overnight city**, with addresses, check-in/out times,
  nights, room type, and costs.
- **Multiple restaurants and activities per city** with categories.
- **Multi-currency costs** (HUF, CZK, EUR, CAD) — Croatia uses EUR (adopted
  in 2023, not HRK).
- **2–3 collaborators** (Maya owner, Anna editor, James editor, Leo commenter)
  with avatars and a comment thread on a few items.
- **Items in each lifecycle status**: `planned`, `booked`, `completed`.
- **Notes with image and PDF attachments** per city.
- **An unresolved decision** (`itm_d07_04` — "Pick a dinner spot for tonight")
  to exercise the "note + open question" UI affordance.
- **A presence record** showing two collaborators currently in the trip,
  one with a cursor inside a notes field.

## Companion files

The same dataset is mirrored as a flat CSV (one row per item) for the
spreadsheet smart-import flow:

- `docs/product/templates/gotravel-template.csv` — empty template with header
  row + a small Lisbon/Porto example.
- `docs/product/templates/gotravel-example-trip.csv` — full Budapest → Prague
  → Croatia trip as a CSV that smart-imports back into a trip equivalent to
  the JSON.

The `.xlsx` multi-sheet versions of both files are deferred to the MVP
build (a Node or Python script in the monorepo will generate them from the
JSON to keep CSV, JSON, and XLSX in sync).

## Notice

This is **sample data for UI development only**. Place names are real;
specific business names, prices, booking references, and contact details
are illustrative and should not be treated as real bookings.

## Updating the fixture

If you change `sample-trip-budapest-prague-croatia.json`, also:

1. Update `docs/product/templates/gotravel-example-trip.csv` to match.
2. Re-run the Storybook story snapshot suite (once it exists).
3. Bump `fixtureVersion` in the JSON header.
4. Mention the change in the PR description so design + sales know.

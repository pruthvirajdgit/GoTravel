# GoTravel Excel/CSV Import Template — Schema

> Status: **Draft v0.1** — defines the canonical shape of files users can
> upload to GoTravel for smart-captured itinerary import. Targets both
> `.xlsx` (multi-sheet, with data validation and dropdowns) and `.csv`
> (single-sheet, minimal).

## Design goals

1. **Demoable in one tap.** A non-technical user can download the template,
   fill in the example rows, upload, and watch a complete trip materialize
   on the map.
2. **Forgiving.** If columns are missing, mislabeled, or out of order, the
   smart-capture pipeline infers as much as it can and shows a preview where
   the user confirms or fixes the mapping before commit.
3. **Lossless round-trip.** Anything in our data model that is reasonable to
   express in a spreadsheet is expressible in the template, and re-uploading
   an exported trip recreates it faithfully.

---

## `.xlsx` (multi-sheet) layout

| Sheet      | Required | Purpose                                                          |
|------------|----------|------------------------------------------------------------------|
| `Trip`     | optional | Trip-level metadata (title, dates, home currency, description).  |
| `Days`     | optional | Per-day metadata (title, notes). Auto-generated if missing.      |
| `Items`    | **yes**  | The itinerary itself. One row per item.                          |
| `Members`  | optional | Collaborators to pre-invite by email.                            |
| `Costs`    | optional | Trip- or day-level costs that are not tied to a specific item.   |
| `README`   | optional | Human-readable column documentation (also published separately). |

If a sheet is missing, GoTravel infers what it can:
- Missing `Trip` → trip title = file name; `start_date`/`end_date` = min/max
  item dates; `home_currency` = current user's home currency.
- Missing `Days` → days are auto-generated from item dates.

---

## `Items` sheet — column reference (canonical order)

| Column            | Type / format            | Required | Notes |
|-------------------|--------------------------|:--------:|-------|
| `day`             | integer (1-based)        |    ✱    | Either `day` OR `date` must be supplied. |
| `date`            | YYYY-MM-DD               |    ✱    | Used when `day` is absent. |
| `start_time`      | HH:MM (24h, local)       |          | |
| `end_time`        | HH:MM (24h, local)       |          | If absent and `duration_min` set, end is computed. |
| `duration_min`    | integer (minutes)        |          | |
| `type`            | enum                     |    ✓    | `lodging` \| `transit` \| `activity` \| `meal` \| `note` |
| `title`           | string                   |    ✓    | |
| `city`            | string                   |          | Helps geocoding when `lat`/`lng` are absent. |
| `location_name`   | string                   |          | e.g. "Hotel Rum Budapest" |
| `address`         | string                   |          | Full street address; used for geocoding. |
| `lat`             | float (-90..90)          |          | If absent, geocoded from address. |
| `lng`             | float (-180..180)        |          | |
| `status`          | enum                     |          | `planned` (default) \| `booked` \| `completed` \| `cancelled` |
| `booking_ref`     | string                   |          | |
| `booking_url`     | URL                      |          | |
| `cost_amount`     | decimal                  |          | In major units (e.g. `125.00`, not `12500`). |
| `cost_currency`   | ISO-4217 (e.g. `EUR`)    |          | Required if `cost_amount` is set. |
| `cost_split`      | enum                     |          | `shared` \| `personal` |
| `notes`           | string                   |          | Long-form notes. |
| `source_url`      | URL                      |          | The original link the entry came from (for re-extraction). |
| `transit_mode`    | enum                     |          | `flight` \| `train` \| `bus` \| `ferry` \| `car` \| `taxi` \| `walk` |
| `carrier`         | string                   |          | e.g. "Air Canada" |
| `vehicle_no`      | string                   |          | Flight number, train number, etc. |
| `from_location`   | string                   |          | Free-text origin for transit; geocoded. |
| `to_location`     | string                   |          | Free-text destination for transit; geocoded. |
| `check_in`        | YYYY-MM-DD HH:MM         |          | Lodging only. |
| `check_out`       | YYYY-MM-DD HH:MM         |          | Lodging only. |
| `nights`          | integer                  |          | Lodging only. Computed if missing. |
| `room_type`       | string                   |          | Lodging only. |
| `guests_count`    | integer                  |          | Lodging only. |
| `meal_type`       | enum                     |          | `breakfast` \| `lunch` \| `dinner` \| `snack` |
| `cuisine`         | string                   |          | |
| `category`        | string                   |          | Activity category (sightseeing, museum, outdoor, nightlife, …). |
| `ticket_required` | boolean                  |          | `true` \| `false` |

Legend: `✓` = required, `✱` = at least one of the asterisk-marked columns is
required.

### Behavior when columns don't match
- Exact match (case-insensitive, ignore spaces/underscores) → bound directly.
- Close-but-not-exact match → bound with a "needs confirmation" flag in the
  preview UI.
- Unknown column → exposed in the preview as "unmapped", with a dropdown to
  bind it to a schema column or to skip.
- Missing required columns → preview shows the affected rows in red with the
  ability to add the missing data inline before commit.

---

## `Trip` sheet (optional)

Two-column key/value sheet:

| Key                 | Value (example)                       |
|---------------------|---------------------------------------|
| `title`             | `Central Europe + Adriatic`           |
| `start_date`        | `2026-06-13`                          |
| `end_date`          | `2026-06-25`                          |
| `home_currency`     | `CAD`                                 |
| `default_timezone` | `Europe/Budapest`                     |
| `description`       | Free-text trip blurb.                 |
| `cover_photo_url`   | URL                                   |

## `Days` sheet (optional)

| Column              | Type            | Notes |
|---------------------|-----------------|-------|
| `day`               | integer         | 1-based. |
| `date`              | YYYY-MM-DD      | Either `day` or `date` must be present. |
| `title`             | string          | e.g. "Arrival in Budapest" |
| `primary_city`      | string          | |
| `notes`             | string          | Long-form notes for the day. |

## `Members` sheet (optional)

| Column         | Type / format                              |
|----------------|--------------------------------------------|
| `email`        | email                                      |
| `display_name` | string                                     |
| `role`         | `owner` \| `editor` \| `commenter` \| `viewer` |

On import, members are invited via email (or, if already a GoTravel user,
added directly).

## `Costs` sheet (optional)

| Column          | Type / format                              |
|-----------------|--------------------------------------------|
| `day`           | integer (optional)                         |
| `label`         | string                                     |
| `cost_amount`   | decimal                                    |
| `cost_currency` | ISO-4217                                   |
| `paid_by`       | email of member                            |

---

## CSV variant

For users who don't want to deal with multi-sheet Excel files, a single CSV
file representing only the `Items` sheet is fully supported. Trip metadata
in this case is captured in the in-app preview step (the user enters trip
title, home currency, etc.).

---

## Data validation built into the `.xlsx`

When we ship the real `.xlsx`, the following dropdowns and validation rules
are pre-baked into the template:

- `type` — dropdown (`lodging`, `transit`, `activity`, `meal`, `note`).
- `status` — dropdown.
- `cost_currency` — dropdown of the top 25 currencies + free-text fallback.
- `cost_split` — dropdown.
- `transit_mode` — dropdown.
- `meal_type` — dropdown.
- `lat` / `lng` — number range validation.
- Conditional formatting highlights rows missing required fields.

---

## Files

| File                                   | Purpose                                   |
|----------------------------------------|-------------------------------------------|
| `gotravel-template.csv`                | Empty CSV with headers + a few example rows. |
| `gotravel-template.xlsx`               | (TODO, MVP build) Multi-sheet template with dropdowns and validation. |
| `gotravel-example-trip.csv`            | Full Budapest → Prague → Croatia trip as a flat CSV (mirrors `fixtures/sample-trip-budapest-prague-croatia.json`). |
| `gotravel-example-trip.xlsx`           | (TODO, MVP build) Same data as the multi-sheet `.xlsx`. |

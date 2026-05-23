# GoTravel Data Model (Draft v0.1)

> Status: **Draft** — written ahead of competitor research; will be refined once
> Phase 1 teardowns confirm/contradict assumptions about how the leaders model
> their data.

This document defines the entities, relationships, and field-level conventions
that the mobile app, backend, and Excel/CSV template all share. It is the
single source of truth for shape; transport (Postgres rows, Firestore docs,
JSON fixtures, spreadsheet columns) is downstream.

## Conventions

- **IDs**: ULIDs (`01HXYZ...`). Lexicographically sortable, URL-safe, 26 chars.
- **Timestamps**: ISO-8601 UTC strings on the wire (`2026-06-12T09:30:00Z`).
  Local time-of-day is stored separately for things like "10:00 check-in" that
  must survive a timezone change without shifting.
- **Money**: stored as `{ amount_minor: integer, currency: ISO-4217 }` (e.g.
  `{ amount_minor: 12500, currency: "EUR" }` = €125.00). Never float.
- **Soft-delete**: every entity has `deleted_at` (nullable). Hard-delete only on
  GDPR request.
- **Audit**: every entity has `created_at`, `updated_at`, `created_by` (User ID).
- **Sync vector**: every entity has `version` (monotonic int) plus `updated_at`
  for last-write-wins fields. CRDT fields (long-form notes) are stored
  separately.

## Entities

### User
```
id              ULID
email           string (unique, lowercase)
display_name    string
avatar_url      string?
home_currency   ISO-4217 (e.g. "USD")
locale          BCP-47 (e.g. "en-US")
created_at, updated_at
```

### Trip
```
id              ULID
title           string                       (e.g. "Central Europe + Adriatic")
cover_photo_url string?
start_date      date                         (trip's first day, local)
end_date        date                         (trip's last day, local)
home_currency   ISO-4217                     (display currency for budgets)
default_timezone IANA                        (e.g. "Europe/Budapest")
description     string?                      (CRDT text)
created_by      User.id
created_at, updated_at, version, deleted_at
```

### TripMember
```
id              ULID
trip_id         Trip.id
user_id         User.id
role            enum("owner","editor","commenter","viewer")
joined_at       timestamp
invited_by      User.id?
```
Constraints: a trip has exactly one `owner` at any time; ownership transfer is
an atomic operation.

### Day
```
id              ULID
trip_id         Trip.id
date            date                          (local to the day's primary city)
sequence        int                           (1-based; matches date order)
title           string?                       (e.g. "Arrival in Budapest")
notes           string?                       (CRDT text)
primary_location Location.id?                 (city-level location for header)
```

### Item
```
id              ULID
trip_id         Trip.id
day_id          Day.id?                       (null = unscheduled)
sequence        int                           (order within the day)
type            enum("lodging","transit","activity","meal","note")
title           string
location_id     Location.id?
start_time      time-of-day? + timezone       (local)
end_time        time-of-day? + timezone       (local)
duration_min    int?                          (alternative to end_time)
status          enum("planned","booked","completed","cancelled")
booking_ref     string?                       (confirmation number)
booking_url     string?
notes           string?                       (CRDT text)
cost            Money?                        (per-item cost)
cost_split      enum("shared","personal")?
source_url      string?                       (the link the user pasted)
extracted_from  enum("manual","link","email","excel")
created_at, updated_at, version, deleted_at
```

#### Item subtypes (carried as a `details` object on `Item`)
- **lodging**: `check_in`, `check_out`, `nights`, `room_type`, `guests_count`
- **transit**: `mode` (`flight`|`train`|`bus`|`ferry`|`car`|`taxi`|`rideshare`|`metro`|`tram`|`walk`),
  `carrier`, `vehicle_no`, `route_number`, `from_location_id`, `to_location_id`,
  `departure_time`, `arrival_time`, `departure_terminal`, `departure_gate`,
  `arrival_terminal`, `arrival_gate`, `seat`, `travel_class`,
  `pickup_time`, `pickup_address` (last two used for `rideshare`/`taxi`/`car` when
  pickup differs from `from_location`/`departure_time`)
- **activity**: `category` (sightseeing, museum, tour, outdoor, nightlife, etc.),
  `duration_min`, `ticket_required`
- **meal**: `meal_type` (`breakfast`|`lunch`|`dinner`|`snack`), `cuisine`
- **note**: free-form

### Location
```
id              ULID
name            string                        (e.g. "Hotel Astoria Budapest")
address         string?
city            string?
country_iso     ISO-3166-alpha2
lat             float (WGS-84)
lng             float (WGS-84)
google_place_id string?                       (caches Places API result)
mapbox_id       string?
timezone        IANA?
created_at
```

### Cost (aggregate / non-item costs)
Items carry their own `cost`. `Cost` rows are used for trip-level or day-level
costs that don't fit an item (e.g. "tips fund", "FX adjustment").
```
id              ULID
trip_id         Trip.id
day_id          Day.id?
item_id         Item.id?
label           string
amount          Money
paid_by         User.id?
split_among     [User.id]?                    (Splitwise-style; post-MVP)
created_at, updated_at, version
```

### Attachment
```
id              ULID
trip_id         Trip.id
item_id         Item.id?
day_id          Day.id?
kind            enum("image","pdf","email","doc")
url             string                        (object storage URL)
filename        string
mime_type       string
size_bytes      int
uploaded_by     User.id
created_at
```

### Comment
```
id              ULID
trip_id         Trip.id
item_id         Item.id?
day_id          Day.id?
author          User.id
body            string                        (CRDT text)
parent_id       Comment.id?                   (threading)
mentions        [User.id]
created_at, updated_at, deleted_at
```

### Presence (ephemeral, not persisted)
Broadcast on the realtime channel only:
```
trip_id, user_id, last_seen_at, focused_entity_id?, cursor_position?, color
```

## Relationships at a glance

```
User 1—N TripMember N—1 Trip
Trip 1—N Day 1—N Item 0..1—1 Location
Trip 1—N Item (unscheduled items have day_id = null)
Item 0..N Attachment
Item 0..N Comment
Trip 0..N Cost
```

## Field-level conflict resolution

| Field type                       | Strategy                |
|----------------------------------|-------------------------|
| Scalars (title, status, time)    | Last-write-wins on `updated_at` |
| Long-form text (notes, body)     | CRDT (Y.js text)        |
| Item ordering within a day       | Fractional indexing (`sequence` as rational) |
| Membership/role                  | Server-authoritative; client mutations are proposals |
| Cost amount                      | LWW with audit trail kept in Comments |

## Open questions for Phase 1 refinement

- Do we need a first-class `Segment` entity for multi-leg transit, or is the
  `from_location`/`to_location` pair on a transit `Item` enough? (TripIt
  separates them; Wanderlog does not.)
- Lifecycle status set — does "booked" warrant sub-statuses (confirmed,
  pending, refunded)?
- Per-user vs per-trip cost visibility (private notes on someone else's item?).
- Do we model "packing list" / "to-do list" as Items with a special type, or
  as a separate Checklist entity?

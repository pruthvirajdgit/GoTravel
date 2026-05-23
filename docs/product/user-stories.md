# GoTravel MVP User Stories (Draft v0.1)

> Status: **Draft** — written against the v0.1 data model and v0.1 personas;
> will be refined once Phase 1 research lands.

Stories are grouped by epic. Each story has acceptance criteria. **P0** = MVP
must-have; **P1** = MVP nice-to-have; **P2** = post-MVP.

---

## Epic 1 — Auth & onboarding

**[P0] Story 1.1 — Sign up / log in**
> As a new user I can create an account using email + password, Google, or
> Apple, so I can start planning a trip in under 60 seconds.

Acceptance:
- Email/password, Google Sign-In, Sign in with Apple all available on both
  platforms (Apple is mandatory on iOS per App Store guidelines).
- After auth I land on the trip list. If empty, I see an empty state with two
  big actions: **"Create a trip"** and **"Import from spreadsheet"**, and a
  smaller debug-mode action **"Load demo trip"**.

**[P0] Story 1.2 — Set home currency**
> As a new user I am asked once for my home currency so trip budgets show in
> a unit I understand.

Acceptance:
- Defaults to a sensible value inferred from device locale.
- Editable later in profile.

---

## Epic 2 — Create & view a trip

**[P0] Story 2.1 — Create a trip manually**
> As any user I can create a trip by entering a title, start date, end date,
> and (optionally) a primary destination, in under 30 seconds.

Acceptance:
- Days are auto-generated for the date range.
- Default timezone inferred from the primary destination if provided.

**[P0] Story 2.2 — Map-first trip home**
> As any user I open a trip and immediately see a map showing all items, with
> a bottom-sheet itinerary list, with the current day's pins highlighted.

Acceptance:
- Map pans/zooms to fit all items on first open.
- Tapping a pin opens the corresponding item; tapping a list row pans to its
  pin.
- A day-selector lets me filter pins to one day or "all days".
- Renders smoothly at 60 fps with 200 markers on a mid-tier Android device.

**[P0] Story 2.3 — Day view**
> As any user I can open a single day and see its items as a timeline, with
> drag-to-reorder.

**[P0] Story 2.4 — Item detail / edit**
> As any user I can tap any item to view and edit all its fields (title,
> location, time, cost, notes, attachments, status).

Acceptance:
- Every editable field has a meaningful default from the canonical fixture
  in dev mode so the UI is never blank.
- Edits sync within 1 second when online.
- Edits are persisted locally and queued when offline; sync on reconnect.

---

## Epic 3 — Multi-modal input (the differentiator)

**[P0] Story 3.1 — Excel / CSV smart import (canonical demo path)**
> As any user I can upload a `.xlsx` or `.csv` file (our template, or my
> own) and have it materialize into a fully-populated trip in seconds.

Acceptance:
- File picker uses the platform document picker.
- Template files (`gotravel-template.xlsx`, `gotravel-template.csv`) are
  downloadable from inside the app and from a public URL.
- After upload, a preview screen shows the inferred column mapping and any
  rows that failed to parse, with inline fix-up.
- If the sheet matches the template exactly, the preview is one-tap-confirm.
- If columns don't match, an LLM-assisted column inference fills the gaps and
  shows confidence per column.
- On confirm, the trip is created with all items, locations geocoded, and
  costs in their declared currencies.

**[P0] Story 3.2 — Paste a link**
> As any user I can paste a hotel/restaurant/POI URL (or use the platform
> share sheet) and have it become a structured itinerary item placed on the
> map.

Acceptance:
- Supports paste from clipboard and platform share extensions (iOS share sheet,
  Android intent).
- Extraction tier 1 (Open Graph / JSON-LD) is attempted on-device-or-edge in
  <2s; tier 2 (LLM) falls back gracefully.
- Preview screen shows the extracted title, location, image, and any
  detected fields, with manual override before commit.
- Source URL is preserved on the item for re-extraction later.

**[P0] Story 3.3 — Manual add with map-search autocomplete**
> As any user I can search for a place by name and add it as an item with
> location, photo, and metadata auto-filled.

---

## Epic 4 — Real-time collaboration

**[P0] Story 4.1 — Invite collaborators by link**
> As an owner I can generate a shareable invite link with a chosen role
> (editor, commenter, viewer) and an optional expiration.

Acceptance:
- Link is revocable from a trip settings screen.
- Recipients without an account are prompted to sign up before joining.
- Owner can change a member's role or remove them at any time.

**[P0] Story 4.2 — Presence**
> As any collaborator I can see who else is currently viewing this trip and
> what they're focused on.

Acceptance:
- Avatar chips in the header show active collaborators (last-seen < 30s).
- Tapping an avatar pans the map to that user's current focus, with their
  cursor color highlighted.

**[P0] Story 4.3 — Live multi-user edit with conflict-free merging**
> As any collaborator I can edit an item while another collaborator edits a
> different item (or even the same item's notes field) and both our edits
> persist without loss.

Acceptance:
- Scalar fields use LWW; long-form notes use CRDT.
- Two devices editing the same item's notes simultaneously both see the
  merged result with no manual conflict resolution.
- Offline edits made on two devices reconcile on reconnect with no data
  loss (see Story 6.2).

**[P1] Story 4.4 — Comments**
> As any collaborator I can leave a comment on an item, optionally
> @-mentioning another collaborator, and they receive a notification.

---

## Epic 5 — Budget tracking

**[P0] Story 5.1 — Per-item cost in any currency**
> As any user I can attach a cost (with currency) to any item, and a
> running total shows in my home currency at the day-level and trip-level.

Acceptance:
- Currency picker defaults to the currency of the item's destination city.
- FX rates are fetched daily and cached; the displayed total notes the FX date.
- Per-trip total and per-day totals are visible without navigating away from
  the map.

**[P1] Story 5.2 — Cost split (read-only in MVP, edit in post-MVP)**
> As any user I can mark an item cost as "shared" so it counts toward an
> implied per-person share. Full Splitwise-style splitting is post-MVP.

---

## Epic 6 — Offline-first

**[P0] Story 6.1 — Full offline read**
> As any user I can open the app with no network and see my full trip,
> including map (cached basemap if available) and all items.

**[P0] Story 6.2 — Offline edit and merge on reconnect**
> As any user I can edit items, reorder days, add notes, and add new items
> while offline; my changes queue locally and sync on reconnect without
> data loss, even if collaborators have edited the same trip in the meantime.

---

## Epic 7 — Notifications

**[P1] Story 7.1 — Push on collaborator events**
> As any collaborator I receive a push notification when (a) someone accepts
> my invite, (b) a comment mentions me, (c) an item I created is edited by
> someone else.

Acceptance:
- Granular per-trip mute is available.

---

## Epic 8 — Demo & dev fixtures (cross-cutting)

**[P0] Story 8.1 — Load canonical demo trip**
> As a developer or sales engineer (in builds with the debug menu enabled)
> I can tap "Load demo data" and have the Budapest → Prague → Croatia trip
> hydrated into my account, ready to drive a demo.

Acceptance:
- The fixture hydrates in <2 seconds.
- The same JSON is bundled into Storybook so every component renders against
  real-looking data.
- CI lints: no merged screen can lack a fixture-backed Storybook story.

---

## Out of MVP (documented to prevent scope creep)

- Email forwarding inbox / OAuth Gmail / Outlook scanning.
- AI itinerary auto-generation ("plan me 5 days in Tokyo").
- Booking / affiliate integrations.
- Full Splitwise-style expense splitting.
- Offline downloadable map tile regions.
- Public/shareable web pages for non-app users.
- Social / discovery feed.

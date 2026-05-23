# GoTravel — MVP Scope (the lean-MVP boundary)

> **Last updated:** 2026-05-23
> **Why this doc exists:** Phases 1 → 4 produced a complete plan for the
> full-featured GoTravel app. On 2026-05-23 the lean-MVP scope was
> tightened. This document is the consolidated record of what's in the
> lean MVP, what's deferred to the post-MVP expansion, and where each
> deferred capability already has design/architecture coverage so it
> ships as a *build*, not a *redesign*, when its time comes.

---

## 1. Two-axis scope

```
                    IN  LEAN MVP                │  POST-MVP EXPANSION
  ──────────────────────────────────────────────┼─────────────────────────────
  Capture            • manual entry + map search │  • Excel / CSV smart import
                     • link-paste extraction     │  • CSV export
                     • Load-Demo (JSON fixture)  │  • email forwarding inbox
                                                 │  • AI itinerary generation
  ──────────────────────────────────────────────┼─────────────────────────────
  Plan               • map-first view + day list │  • offline map regions
                     • item detail + edit        │  • public/shareable trip pages
                     • start-of-journey times    │  • web companion (read-mostly)
                     • single-currency budget    │  • multi-currency + FX
  ──────────────────────────────────────────────┼─────────────────────────────
  Collaborate        • invite-link sharing       │  • live presence + cursors
                     • role-based access         │  • "X is editing" indicators
                     • LWW eventual-consistency  │  • threaded comments
                                                 │  • @-mention notifications
  ──────────────────────────────────────────────┼─────────────────────────────
  Travel             • local fixed-offset        │  • ETA-based smart reminders
                       reminders (5 min default) │    (GPS + Mapbox Directions
                                                 │     + 10-min buffer logic)
                                                 │  • day-of "trip mode" +
                                                 │    tracking handoff
                                                 │  • trip recap / printed book
  ──────────────────────────────────────────────┼─────────────────────────────
  Platform           • iOS + Android (RN+Expo)   │  • wearables / CarPlay
                                                 │  • Android Auto
```

---

## 2. What's in the lean MVP

### Capture
- Manual item entry with Mapbox Search Box autocomplete
- Link-paste extraction (OG / JSON-LD first, GPT-4o-mini fallback)
- "Load demo data" debug toggle hydrates the canonical
  Budapest → Prague → Croatia fixture — **this is the demo path**

### Plan
- Trip → days → items → locations → costs (per `data-model.md`)
- Map-first trip view (mockup: `08-trip-map-view.svg`)
- Day list view (mockup: `09-trip-day-view.svg`)
- Item detail with edit/delete (mockup: `10-item-detail.svg`)
- **Start-of-journey UI** — prominent countdown on home, per-item
  `start_time` + duration, computed travel-time chips between consecutive
  items (straight-line distance × mode-typical-speed, no live routing
  API call yet), trip-timezone-aware throughout
- **Single-currency budget** — trip-level home currency only

### Collaborate (eventual-consistency only — no realtime)
- Invite-link sharing with role-based access (owner / editor / viewer)
- Multi-device, multi-account editing of the same trip with **last-write-wins**
  (no live cursors, no presence avatars, no "X is editing", no comments)

### Travel
- **Local OS notification reminders** scheduled X minutes before each
  `item.start_time` (default 5 min, user-configurable per item or globally)
- Pure on-device scheduling via `expo-notifications` — no server, no
  network, no location permission needed
- Quiet hours respected; reminders cancel and reschedule on item edit

### Foundations
- Auth (email + Google + Apple via Supabase Auth)
- Cloud sync via PowerSync (Supabase Postgres ↔ on-device SQLite)
- Offline-first local cache (op-sqlite + Drizzle + SQLCipher)
- Push for shared-trip changes (FCM/APNs via Supabase Edge Functions)
- PostHog + Sentry; EAS Build

---

## 3. What's deferred — and where it's already speced

Every deferred capability has design or architecture coverage so that
when we build it, it's well-understood scope.

| Capability | Todo | Mockups | Architecture |
|------------|------|---------|--------------|
| Live presence / cursors / comments / @-mentions | `p8-realtime-collab` | `14-live-presence.svg`, `15-comments-mentions.svg` | `realtime-protocol.md` (full Yjs + Presence protocol), `stack-decision.md` §4 |
| Multi-currency + FX | `p8-multi-currency` | `12-budget-summary.svg` shows the target UI with HUF/CZK/EUR breakdown | `data-model.md` already includes per-item `currency` field |
| Excel/CSV smart import | `p5-spike-excel` → `p6-excel-import` | `05a-excel-upload.svg`, `05b-excel-column-mapping.svg`, `05c-excel-import-success.svg` | `stack-decision.md` (SheetJS + LLM column-inference) — templates already published in `docs/product/templates/` |
| CSV export | `csv-export` | — | reverse of the import pipeline |
| ETA-based smart reminders | `p8-smart-reminders` | — (logic, not UI) | Spec to be written; will use Mapbox Directions API + background location |
| Day-of "trip mode" + tracking handoff | `p8-planning-tracking-handoff` | `17-trip-mode-active.svg` | TBD |
| Trip recap | (under `p8-post-mvp`) | `18-trip-recap.svg` | TBD |
| Email inbox / AI generation / booking / wearables / web / social | `p8-post-mvp` | — | TBD when prioritized |

---

## 4. Impact on the de-risking spikes

The originally-planned five Phase-5 spikes were carved down to **three**:

| Spike | Status |
|-------|--------|
| `p5-spike-map` — 500-marker map perf | ✅ keep — biggest single risk |
| `p5-spike-link` — link extraction quality | ✅ keep |
| `p5-spike-offline` — offline-edit + reconnect | ✅ keep, **simplified** (no Yjs in MVP, PowerSync LWW only) |
| `p5-spike-collab` — live multi-user editing | ⏸ deferred to `p8-realtime-collab` build |
| `p5-spike-excel` — Excel smart ingestion | ⏸ deferred to `p6-excel-import` build |

---

## 5. Reminder logic — MVP vs expansion

This was called out specifically in the 2026-05-23 scope conversation.

### Lean MVP — fixed-offset local reminder
```
fire_at = item.start_time - reminder_offset_minutes  // default 5
                ↓
        expo-notifications.scheduleNotificationAsync({ trigger: { date: fire_at } })
```
- Scheduled when the item is created or edited; cancelled on delete.
- No location permission, no Directions API call, no background work.
- User-configurable offset per-item and globally.
- Quiet-hours respected.

### Post-MVP — ETA-based smart reminder
```
on reminder_window_open (e.g. T-30 min):
    travel_time = Mapbox.Directions(from: user.gps, to: item.location, mode: …)
    fire_at = item.start_time - travel_time - 10_minute_buffer
    schedule_or_reschedule(fire_at)
```
- Requires foreground or coarse-background location permission.
- Falls back gracefully to the fixed-offset version when permission denied
  or routing fails.
- Notification actions: "Leave now", "I'm already there", "Snooze 5 min".
- Battery-conscious strategy: only computes within the reminder window,
  not continuously.
- Full logic to be designed under `p8-smart-reminders` before build.

---

## 6. Cross-references

- Plan: `~/.copilot/session-state/.../plan.md` §2 (Goals & Non-Goals — revised),
  §14–16 (progress + runway)
- Stack: `docs/architecture/stack-decision.md` (MVP carve-out banner at top)
- Realtime protocol: `docs/architecture/realtime-protocol.md` (MVP-scope
  banner at top — full protocol kept for the post-MVP build)
- Mockups: `design/mockups/` — screens flagged POST-MVP on the artboard
  itself when applicable (17, 18); screens 14, 15, 05a–c, and 12's
  multi-currency breakdown are MVP-deferred but the SVGs remain
- Todos: session SQL `todos` table — `p8-*` and the two deferred
  `p5-spike-*` rows track the expansion-layer work

# GoTravel Offline-First Strategy

> Status: v1.0 (Phase 4). Source of truth for the stack is
> `stack-decision.md`; for the sync wire protocol, `realtime-protocol.md`.

## Goal

The mobile app **never blocks on the network for any user-facing action**.
Open a trip on the plane, edit, reorder days, add costs, and have it all
sync seamlessly on reconnect with no data loss and no manual conflict UI
in the common case.

## Local stack

| Layer                                 | Tech                                                                                        |
|---------------------------------------|---------------------------------------------------------------------------------------------|
| Storage engine                        | SQLite via `op-sqlite` (JSI, fastest RN binding); **SQLCipher AES-256 encryption enabled**   |
| Schema + queries                      | **Drizzle ORM** — schema shared with Supabase Postgres; type-safe queries end-to-end         |
| Sync engine (server of record)        | **PowerSync** — replicates Postgres ↔ local SQLite under RLS, with offline change journal    |
| Live-edit CRDT (when online)          | **Yjs Y.Doc per trip**, buffered locally in the SQLite `yjs_state` sidecar table when offline |
| Presence / cursors (when online)      | **Supabase Realtime Presence** — pure ephemeral; nothing to persist when offline             |
| Map tiles                             | Mapbox **online tiles only** in MVP (offline tile regions are post-MVP)                      |
| Attachments                           | Supabase Storage + R2 CDN; uploads queued via the change journal; reads cached on device     |

## Local schema (Drizzle)

The mobile-side schema mirrors Postgres 1:1, with two extra columns on
every domain table:

| Column          | Type          | Purpose                                                                            |
|-----------------|---------------|------------------------------------------------------------------------------------|
| `sync_status`   | text          | `synced` \| `pending` \| `conflict` — PowerSync manages this                       |
| `client_updated_at` | timestamp | When this row was last touched locally; used for tiebreaking in LWW                |

And a sidecar table:

```sql
CREATE TABLE yjs_state (
  trip_id        TEXT PRIMARY KEY,
  doc_state      BLOB NOT NULL,    -- Yjs.encodeStateAsUpdate(doc)
  doc_version    INTEGER NOT NULL, -- monotonic, bumped on every local update
  last_synced_at INTEGER           -- ms since epoch; null until we've round-tripped
);
```

## Data flow (from `tech-landscape.md` App. B)

```
User Action (add pin, edit hotel, filter day)
        │
        ▼
Yjs Y.Doc (CRDT document for the trip)
        │  ← applied locally, no network needed
        ▼
Drizzle ORM → op-sqlite (local SQLite, SQLCipher-encrypted)
        │  ← persisted to disk immediately
        │
        ├── [ONLINE] ────────────────────────────────────────────┐
        │              PowerSync pushes SQLite delta → Postgres  │
        │                                                        │
        │              Yjs update broadcast →                    │
        │              y-websocket (Fly.io) → other collaborators│
        │                                                        │
        └── [OFFLINE] ───────────────────────────────────────────┘
               Changes queue in SQLite with sync_status='pending'
               Yjs offline state buffered in yjs_state.doc_state
               On reconnect: PowerSync auto-reconciles
               Yjs CRDT merges concurrent offline edits — no data loss
```

## Reconnect flow (step by step)

1. **Detect connectivity** via Expo `NetInfo`. On regain, fire `onResume`.
2. **PowerSync first** — flush every `sync_status='pending'` row to
   Postgres. PowerSync handles batching and retries; we don't write that
   code.
3. **Yjs second** — for each open trip, connect to y-websocket. The
   server sends the authoritative state vector; the client sends a sync
   step encoding everything missing. The merge is automatic.
4. **Pull anything new** — PowerSync pulls down rows changed by other
   users while we were offline. UI re-renders from the SQLite store.
5. **Presence last** — rejoin the `presence:trip:<id>` channel and
   broadcast our state. Other clients' presence is refreshed.

The user sees a single, brief "Syncing…" indicator that goes away in
under a second on a normal reconnect.

## Conflict resolution

### Common case: no visible conflict
Almost every realistic offline scenario resolves automatically:

- Two users edit different fields → both applied (LWW per field).
- Two users edit the same `Y.Text` (notes) → CRDT merges.
- Two users reorder the same `Y.Array` (day order) → CRDT merges.
- One user adds an item; another reorders the same day → both applied.
- Multiple cost edits → last-write-wins per row, both users see the
  latest value with a subtle "edited 3s ago by Anna" indicator (driven
  by the `client_updated_at` column).

### The two cases that **can** require human resolution

1. **Edit-after-delete.** User A deletes an item while offline; User B
   edits the same item while offline.
   - Resolution: the edit wins (soft-delete is overwritten back to live).
   - UI: in the trip's "Recent activity" feed, a row appears: "Anna
     edited an item that James deleted. The item is restored." User can
     re-delete with one tap.

2. **Currency change on an item with multiple offline cost edits.**
   - Resolution: the latest currency wins; the cost amount is **not**
     auto-converted. A banner appears on that item: "Currency changed to
     CZK by Anna while you were offline. The amount (12,000) was
     previously in HUF. Tap to confirm or convert."

These are the only two cases that exist in our domain model that aren't
cleanly auto-mergeable.

## Storage budget

| Trip size                              | Approx. local footprint                                |
|----------------------------------------|--------------------------------------------------------|
| Small (3 days, 10 items, 0 attachments)| < 100 KB SQLite + ~5 KB Yjs state                      |
| Canonical fixture (13 days, 41 items)  | ~600 KB SQLite + ~50 KB Yjs state + ~1 MB cached photos |
| Large (30 days, 300 items, 50 photos)  | ~4 MB SQLite + ~300 KB Yjs state + ~50 MB cached photos|

We cap the per-trip cached-attachment budget at **100 MB** and evict on
LRU. Users can pin a trip to force "keep everything" (with a warning
about device storage).

## What is **not** offline in the MVP

| Feature                  | Why                                                                                 |
|--------------------------|-------------------------------------------------------------------------------------|
| Map tiles outside cache  | Mapbox offline tile regions require additional SDK config + licensing; post-MVP     |
| Link-paste extraction    | Needs LLM + HTTP fetch; on-device fallback via Apple Foundation Models is post-MVP  |
| Spreadsheet column inference fallback | Same — on-device tier-1 (header alias match) **does** work offline       |
| Push notifications       | By definition require connectivity                                                  |
| Geocoding                | Mapbox / Google Places HTTP — cached results are reused but new lookups fail offline|
| Joining a new trip       | Requires Supabase auth + initial PowerSync hydration                                |

## Testing

- **Manual:** every PR that touches sync logic must include a "kill the
  network" smoke test described in the PR template.
- **Automated:** Spike D (Phase 5) becomes a permanent integration test —
  two emulators with a scripted network outage produce a known
  Y.Doc state vector and assert convergence.

## Open question

- Should we offer a "this trip is critical — keep tiles offline too"
  mode in the MVP for power users, even though general offline tiles
  are post-MVP? Tracked under Phase 6 polish.

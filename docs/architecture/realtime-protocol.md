# GoTravel Realtime Sync Protocol

> Status: v1.0 (Phase 4). Defines the wire-level semantics of the three
> realtime components picked in `stack-decision.md` §4.

## Three components, three roles

| Component                  | Role                                                                                | Persistence       |
|----------------------------|-------------------------------------------------------------------------------------|-------------------|
| **PowerSync**              | Replicate Supabase Postgres ↔ local SQLite (server of record sync)                  | Postgres          |
| **Yjs over y-websocket**   | Live multi-user editing of co-edited fields (notes, ordered item lists)             | Snapshot → Postgres at session close |
| **Supabase Realtime Presence** | "Who is here, where is their cursor, what are they typing"                     | None — ephemeral  |

The reason we use three rather than one is that they have **three
different consistency models** and any one of them used for all three
roles is a bad fit (either too heavy, too loose, or unable to express
presence cleanly).

---

## 1. PowerSync — server-of-record sync

### Channels / topic shape

PowerSync syncs **all rows the current user is authorized to see** under
Supabase Row-Level Security. Authorization is re-evaluated on every sync;
joining or leaving a trip changes the set of rows replicated.

The "sync rule" (PowerSync's bucket configuration) for GoTravel:

```yaml
bucket_definitions:
  user_buckets:
    parameters: |
      SELECT id AS user_id FROM users WHERE id = request.user_id()
    data:
      - SELECT * FROM users WHERE id = bucket.user_id

  trip_buckets:
    parameters: |
      SELECT trip_id FROM trip_members WHERE user_id = request.user_id()
    data:
      - SELECT * FROM trips        WHERE id      = bucket.trip_id
      - SELECT * FROM trip_members WHERE trip_id = bucket.trip_id
      - SELECT * FROM days         WHERE trip_id = bucket.trip_id
      - SELECT * FROM items        WHERE trip_id = bucket.trip_id
      - SELECT * FROM locations    WHERE trip_id = bucket.trip_id
      - SELECT * FROM costs        WHERE trip_id = bucket.trip_id
      - SELECT * FROM attachments  WHERE trip_id = bucket.trip_id
      - SELECT * FROM comments     WHERE trip_id = bucket.trip_id
```

### Write path

Mobile-app writes go to local SQLite first (via Drizzle). PowerSync
detects the change and pushes it to Postgres on the next sync window
(or immediately when online).

### Conflict resolution at this layer

**Last-write-wins per row**, scoped to the row's primary key. This is
acceptable because the per-field co-edit cases (notes, item ordering)
are handled by the Yjs layer, not by PowerSync.

Special case: **deletes**. We use **soft deletes** (`deleted_at` column,
NULL = live). A concurrent edit + delete results in the edit being
preserved on the row (server-side); the delete is later resolved via the
"trash" UI ("3 items were edited after they were deleted — restore or
purge?").

---

## 2. Yjs / y-websocket — live multi-user editing

### Document layout

Each trip has **one Y.Doc**, identified by the trip ID. The Y.Doc
contains:

| Y type            | Key                          | What it holds                                                        |
|-------------------|------------------------------|----------------------------------------------------------------------|
| `Y.Map`           | `meta`                       | Trip-level scalar fields under active co-edit (title, dates, summary)|
| `Y.Map`           | `items.<itemId>`             | Per-item co-edited scalar fields (name, time, price.amount_minor)    |
| `Y.Text`          | `items.<itemId>.notes`       | The free-text notes field on an item                                 |
| `Y.Array`         | `days.<dayId>.itemOrder`     | The order of items inside a day                                      |
| `Y.Array`         | `tripOrder`                  | The order of days inside the trip                                    |
| `Y.Map`           | `comments.<commentId>.body`  | Per-comment body, if a comment is being co-edited                    |

Fields **not** in the Y.Doc (and therefore LWW via Postgres + PowerSync):

- Location coordinates (`Location.lat`, `Location.lng`) — once geocoded
  they don't change; a re-geocode is a deliberate edit.
- Cost currency — changing currency is a destructive operation and gets
  its own UI prompt.
- Attachment metadata — file uploads commit through Supabase Storage
  with a single writer.
- User identity, role assignments — pure server-side mutations.

### Wire protocol

We use the standard `y-websocket` protocol (binary frames over WebSocket).
Each frame is a Yjs update encoded with `lib0/encoding`. Reference:
<https://github.com/yjs/y-websocket>.

### Room / auth

- **Room name:** `trip:<tripId>`.
- **Auth:** the client opens the WebSocket with the Supabase JWT in the
  `Sec-WebSocket-Protocol` header. The Fly.io service validates the
  signature against the Supabase JWKS and looks up
  `SELECT 1 FROM trip_members WHERE trip_id = $1 AND user_id = $2 AND role IN ('owner','editor')`.
  Viewers do not get a y-websocket connection (they receive the latest
  Postgres snapshot via PowerSync only).
- **Disconnect on revocation:** when role changes (member removed,
  demoted to viewer), the Edge Function that performs the mutation
  publishes a `revoke` message on the trip's Supabase Realtime channel,
  which the y-websocket service listens to and uses to close the
  offending socket.

### Snapshot to Postgres

A **debounced server-side Yjs persistence worker** runs on Fly.io
alongside the y-websocket service. Every 5 seconds of idle (or every 30
seconds of activity, whichever comes first), the worker:

1. Reads the current Y.Doc state.
2. Decodes the relevant Y types into Postgres-shaped rows.
3. Writes the rows back to Supabase Postgres via a service-role REST call.
4. PowerSync then re-syncs those rows to all clients (including the
   originating ones — clients reconcile because PowerSync's row version
   matches what the Y.Doc already shows).

This pattern keeps Postgres as the system of record without forcing every
keystroke through the relational DB.

### Conflict resolution at this layer

Y.Doc CRDT semantics — there are no conflicts at the application level.
Two users typing into the same `Y.Text` produce a deterministic merged
result.

---

## 3. Supabase Realtime Presence — ephemeral state

### Channels

One Supabase Realtime channel per trip: `presence:trip:<tripId>`.

### Presence payload

```typescript
type PresenceState = {
  userId:          string;            // ULID
  displayName:     string;
  avatarUrl:       string | null;
  selectedItemId:  string | null;     // currently-focused item, if any
  selectedDayId:   string | null;     // currently-focused day, if any
  mapViewport:     { lat: number; lng: number; zoom: number } | null;
  cursor:          { x: number; y: number } | null; // map-overlay cursor coords
  lastSeenAt:      string;            // ISO 8601
};
```

### Broadcast (non-presence) messages on the same channel

Used for transient signals that don't belong in either Postgres or the Y.Doc:

| Event              | Payload                                                       | Purpose                                                                    |
|--------------------|---------------------------------------------------------------|----------------------------------------------------------------------------|
| `typing`           | `{ userId, itemId, field }`                                   | "Anna is editing the title of item X" — drives the indicator dot           |
| `peekItem`         | `{ userId, itemId }`                                          | "Maya opened the item detail sheet" — drives the avatar bubble on the pin  |
| `bumpToTopOfMap`   | `{ userId, viewport }`                                        | "Anna jumped to Prague" — others optionally follow                         |
| `revokeRoom`       | `{ tripId }` (published by Edge Functions, consumed server-side) | Tell y-websocket to disconnect sockets whose membership was revoked     |

All broadcast messages include the sender's `userId` so clients can
filter their own echoes.

### Authorization

Supabase Realtime Presence channels are gated by RLS: only clients with
membership in `trip_members` for the trip can `subscribe` to
`presence:trip:<tripId>`. The membership check is enforced server-side by
Supabase's Realtime authorization layer.

---

## Per-field consistency reference

| Field on an item                 | Owned by                  | Notes                                                |
|----------------------------------|---------------------------|------------------------------------------------------|
| `title`                          | Yjs (`Y.Map`)             | LWW per character via CRDT; debounce snapshot to PG  |
| `notes`                          | Yjs (`Y.Text`)            | Rich CRDT merge — two-user typing produces sane text |
| `startsAt`, `endsAt`             | Yjs (`Y.Map`)             | Scalar LWW within Yjs; debounce snapshot to PG       |
| `cost.amount_minor`              | Yjs (`Y.Map`)             | Scalar LWW within Yjs                                |
| `cost.currency`                  | Postgres + PowerSync (LWW)| Destructive change; UI confirms before commit        |
| `location.lat`, `location.lng`   | Postgres + PowerSync (LWW)| Set once at geocode; re-geocode is deliberate        |
| `dayId` (which day the item is on)| Yjs (`Y.Array` reorder)  | The `itemOrder` array is the source of truth         |
| `status` (planned/booked/done)   | Yjs (`Y.Map`)             | Scalar LWW within Yjs                                |
| `attachments[]` (file list)      | Postgres + PowerSync (LWW)| Single-writer (upload completes server-side)         |
| `comments` (list)                | Postgres + PowerSync      | Append-only; per-comment body can promote to Yjs when actively edited |

---

## Failure modes

| Scenario                                                  | Behavior                                                                            |
|-----------------------------------------------------------|-------------------------------------------------------------------------------------|
| y-websocket unreachable                                   | Fall back to PowerSync-only mode; UI shows a discreet "sync degraded" indicator. Edits are still persisted locally; co-editors see them only after the snapshot worker writes them to PG and PowerSync replicates. |
| PowerSync unreachable                                     | Local SQLite continues serving the UI; writes queue with `sync_status='pending'`. y-websocket continues to broadcast live CRDT updates among connected peers. |
| Supabase Realtime unreachable                             | Presence indicators disappear; live editing and durable sync continue. Banner: "Live collaboration features unavailable — your edits are still saving." |
| Both PowerSync and y-websocket unreachable simultaneously | Fully offline mode. See `offline-sync.md`.                                          |
| User loses role mid-session                               | `revokeRoom` is broadcast; y-websocket closes the socket; PowerSync's next sync removes the trip's rows from the local store. |

---

## Why not just put everything in Yjs (and skip PowerSync)?

We considered it. Reasons we use Postgres + PowerSync as the system of
record instead:

- **Auth + RLS are easier on rows** than on CRDT document branches.
- **Server-side analytics, exports, and the (post-MVP) email-import path**
  all want a relational store.
- **Mobile RAM** — large trips with 100+ items and many edits would push
  the Y.Doc into multi-MB territory; keeping the durable copy in Postgres
  + lazily hydrating the Y.Doc per active item keeps memory bounded.

## Why not just use Supabase Realtime for everything (and skip Yjs)?

- Supabase Realtime broadcast doesn't merge concurrent edits to a free-
  text field; we'd have to ship our own conflict UI for notes.
- Supabase Realtime presence is great, but it's not a CRDT — the
  "ordered list" of items in a day is genuinely better expressed as a
  `Y.Array`.

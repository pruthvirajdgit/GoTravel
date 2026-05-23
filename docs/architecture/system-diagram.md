# GoTravel System Architecture

> Status: v1.0 (Phase 4). Source of truth = `docs/architecture/stack-decision.md`.
> If any pick in stack-decision.md changes, update this diagram and the
> three sibling docs (`realtime-protocol.md`, `offline-sync.md`,
> `security-and-privacy.md`).

## System diagram (ASCII)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                         GoTravel Mobile App                                  │
│                  (React Native + Expo, New Architecture)                     │
│                                                                              │
│   ┌────────────────────┐   ┌────────────────────┐   ┌────────────────────┐   │
│   │  Mapbox Maps SDK   │   │  Yjs Y.Doc(s)      │   │  Drizzle ORM       │   │
│   │ (rendering, search)│   │ (CRDT per trip)    │   │  → op-sqlite       │   │
│   │  + Google Places   │   │                    │   │   (SQLCipher on)   │   │
│   │  fallback (POIs)   │   │                    │   │                    │   │
│   └─────────┬──────────┘   └─────────┬──────────┘   └─────────┬──────────┘   │
│             │                        │                        │              │
│             │                        │                        │              │
│   ┌─────────▼────────────────────────▼────────────────────────▼─────────┐    │
│   │                       Provider/DAO interfaces                       │    │
│   │   MapProvider   CollabClient   LocalStore   AuthClient   PushClient │    │
│   └─────────┬────────────┬─────────────┬──────────────┬──────────────┬──┘    │
└─────────────┼────────────┼─────────────┼──────────────┼──────────────┼───────┘
              │            │             │              │              │
              ▼            ▼             ▼              ▼              ▼
       ┌──────────┐ ┌────────────┐ ┌──────────┐ ┌────────────┐ ┌────────────┐
       │  Mapbox  │ │ y-websocket│ │ PowerSync│ │  Supabase  │ │ Expo Push  │
       │ (tiles + │ │  on Fly.io │ │   Cloud  │ │  Realtime  │ │  Service   │
       │ Search)  │ │  (CRDT     │ │ (sync    │ │ (presence/ │ │ (→ APNs +  │
       │  + Goog. │ │  document  │ │ engine,  │ │  cursors / │ │   FCM)     │
       │  Places  │ │  state)    │ │ $49/mo)  │ │ broadcast) │ │            │
       └──────────┘ └─────┬──────┘ └────┬─────┘ └─────┬──────┘ └─────┬──────┘
                          │             │             │              │
                          │             ▼             │              │
                          │      ┌──────────────────────────────┐   │
                          └─────►│   Supabase (eu-central-1)    │◄──┘
                                 │                              │
                                 │  Postgres + RLS + PostGIS    │
                                 │  Auth (Email + Google + Apple)│
                                 │  Storage (+ Cloudflare R2 CDN)│
                                 │  Edge Functions (Deno):       │
                                 │    • extractUrl()             │
                                 │    • inferColumns()           │
                                 │    • dispatchPush()           │
                                 │    • signInvite() / verify()  │
                                 └──────┬──────────┬─────────────┘
                                        │          │
                                        ▼          ▼
                              ┌──────────────┐  ┌───────────────┐
                              │ OpenAI       │  │ Cloudflare R2 │
                              │ GPT-4o-mini  │  │ (CDN in front │
                              │ (extraction  │  │  of Supabase  │
                              │  + column    │  │  Storage)     │
                              │  inference)  │  │               │
                              └──────────────┘  └───────────────┘

                              ┌─────────────────────────────────┐
                              │  Observability                  │
                              │  • Sentry (mobile crash + perf) │
                              │  • PostHog (events + flags)     │
                              │  • Supabase built-in logs       │
                              └─────────────────────────────────┘
```

## Component responsibilities

| Component                | Owns                                                                                                  |
|--------------------------|-------------------------------------------------------------------------------------------------------|
| Mobile app               | All UI; local Yjs documents; local SQLite; provider interfaces                                        |
| Mapbox SDK               | Tile rendering, vector layers, autocomplete (Search Box API)                                          |
| Google Places API        | POI metadata fallback (business hours, phone, reviews) in regions where Mapbox/OSM data is thin       |
| Yjs + y-websocket        | Live multi-user editing of trip items, ordered lists, free-text notes (CRDT document state)           |
| PowerSync Cloud          | Server-of-record sync: replicate Supabase Postgres ↔ local SQLite, including offline change journal   |
| Supabase Realtime        | Presence (who's online), cursors, broadcast for ephemeral state (typing indicators, "user opened X")  |
| Supabase Postgres        | Durable system of record (users, trips, days, items, locations, costs, attachments, comments)         |
| Supabase Auth            | Email + Google + Apple sign-in; magic-link fallback; session JWTs                                     |
| Supabase Storage + R2    | Trip attachments (photos, PDFs); R2 CDN fronts read paths to zero-out egress                          |
| Supabase Edge Functions  | Server-side logic: link extraction, spreadsheet column inference, push dispatch, invite-link signing  |
| OpenAI GPT-4o-mini       | Terminal-stage LLM for URL extraction (tier 4) and spreadsheet column inference (low-confidence path) |
| Expo Push Service        | APNs + FCM dispatch with token rotation                                                               |
| Sentry / PostHog         | Crash + performance + product analytics + feature flags                                               |

## Data ownership rules

1. **Supabase Postgres is the system of record.** Everything else is a
   cache or a real-time channel.
2. **The Yjs document is the source of truth for any field that is being
   actively co-edited** until the trip closes its collab session. At
   session close, the Yjs document is canonicalized into Postgres rows
   via PowerSync's normal sync path.
3. **The local SQLite store is the only thing the UI reads from
   synchronously.** PowerSync hydrates it; the UI never blocks on a
   network call to render.
4. **Presence is ephemeral.** Supabase Presence state is never persisted.
   If a user goes offline, their presence is removed.

## Cross-cutting concerns

- **Auth flow:** Supabase Auth issues a JWT; the mobile app sends it on
  every Realtime, Edge Function, PowerSync, and REST request. The same
  JWT is what the y-websocket Fly.io service validates before joining a
  room (it validates the Supabase JWT signature using the project's JWKS).
- **Region:** Single Supabase region (eu-central-1) for the MVP. PowerSync
  and y-websocket sit in regions co-located with the user; both validate
  against the same Supabase JWT.
- **Observability** events from all paths funnel into Sentry (crashes,
  errors, transaction perf) and PostHog (product events). Edge Function
  logs land in Supabase's log explorer.

## Open questions / deferred

- Multi-region Postgres (read replicas in us-east-1, ap-southeast-1) — not
  needed until we have meaningful non-EU traffic.
- Self-hosting y-websocket on our own k8s instead of Fly.io — not needed
  until Fly.io pricing or reliability becomes a concern.
- Whether to offer self-host of the entire stack to enterprise customers —
  out of scope until a B2B motion is on the roadmap.

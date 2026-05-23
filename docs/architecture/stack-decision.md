# GoTravel Stack Decision (v1.0)

> Status: **Concrete picks for MVP.** Sourced from Phase 1 research
> (`docs/research/tech-landscape.md`, `docs/research/framework-recommendation.md`).
> Picks are revisited at the end of each Phase 5 de-risking spike.

## How this document is used

This is the single canonical record of "what we use and why" for GoTravel.
Every layer below has a **chosen option**, the **rationale**, the
**rejected alternatives** with one-line reasons, and the **swap cost** if we
need to migrate later.

If you change any decision below, also:
1. Update the architecture diagram (`system-diagram.png`).
2. Note the change in the relevant ADR (Architecture Decision Record) — we
   keep ADRs in `docs/architecture/decisions/NNNN-*.md`.
3. Bump the version of this document and the date below.

**Version:** 1.0 · **Last updated:** May 2026 (Phase 1 research complete)

---

## Decision criteria (apply uniformly to every layer)

1. **Lean MVP economics** — bias toward managed services with free/cheap
   tiers; pay-as-you-go is fine; large fixed minimums are not.
2. **Swap cost** — favor options where we can swap providers without
   re-architecture. Abstraction layers where the boundary is well-known.
3. **Mobile SDK quality** — iOS + Android first-class, not an afterthought.
4. **Real-time + offline UX** — every layer must support the offline-first +
   realtime requirements of a planning app.
5. **Global from day one** — multi-region, multi-currency, multi-language
   capable.
6. **Hiring market** — favor ecosystems with deep talent pools so we are not
   the only company hiring for an obscure stack.
7. **Vendor risk** — avoid vendors with public sustainability concerns
   (recent layoffs, acquisitions in limbo, repos archived).

---

## Decision template (per layer)

```
### Layer
**Chosen:** TBD
**Rationale (3 bullets):**
- …
- …
- …
**Rejected alternatives:**
- X — reason
- Y — reason
**Swap cost if we change later:** Low / Medium / High — one-line explanation
**Linked research:** docs/research/…
```

---

## Layers

### 1. Cross-platform mobile framework
**Chosen:** **React Native + Expo (SDK 50+)**, with the New Architecture
(Fabric + JSI) on day one.
**Rationale:**
- JavaScript/TypeScript hiring pool is the largest mobile-capable pool —
  decisive for a lean team.
- Real-time + CRDT ecosystems (Yjs, y-websocket, PowerSync, Supabase
  Realtime) are JS-first; Dart bindings trail.
- Expo SDK covers every native module we need (camera, file picker, share
  sheets, deep links, push, secure storage). EAS Build + Submit is a
  strictly better pipeline for our team size than Fastlane.
**Rejected alternatives:**
- Flutter — smaller hiring pool; map performance edge is irrelevant for our
  map-tile-rendering-in-native-layer workload; CRDT ecosystem is weaker.
- Dual-native (SwiftUI + Jetpack Compose) — 2× engineering capacity needed.
**Swap cost if we change later:** **High** — a full rewrite of the app
shell; backend, data model, and Edge Functions survive.
**Linked research:** `docs/research/framework-recommendation.md`,
`docs/research/tech-landscape.md` "RN vs Flutter" trade-offs.
**Gate that could revisit this:** Spike A (60fps with ~130 markers on a
mid-tier Android).

### 2. Map provider
**Chosen:** **Mapbox Maps SDK** (iOS v11 / Android v11) for rendering +
**Mapbox Search Box** for autocomplete, with **Google Places API** wired in
as a POI metadata fallback (business hours, phone numbers, reviews) for
Southeast Asia, Africa, and parts of Latin America where Mapbox/OSM data
quality is weaker.
**Rationale:**
- Free up to 25K MAU and predictable MAU-based pricing above that.
- Custom-styling support is best-in-class; we need a distinctive map
  brand.
- The MapLibre fork (BSD 2-Clause, style-spec-compatible) is a documented
  migration path at scale — see Appendix A of `tech-landscape.md`.
**Rejected alternatives:**
- Google Maps Platform as the primary renderer — best POI data globally,
  but expensive at scale and locks us in.
- MapLibre + Stadia Maps from day one — cheaper long-term but the
  developer experience is weaker for the MVP build.
**Swap cost if we change later:** **Medium** — Mapbox → MapLibre is
near-drop-in (same style spec, same SDK shape); Mapbox → Google would
require non-trivial rework. We isolate map calls behind a thin
`MapProvider` interface so the surface is small.
**Linked research:** `docs/research/tech-landscape.md` App. A.
**Gate that could revisit this:** Mapbox MAU pricing trajectory; if we
trend past 100K MAU on a free product, migrate to MapLibre + Stadia.

### 3. Backend / BaaS
**Chosen:** **Supabase** (Postgres + Realtime + Auth + Storage + Edge
Functions), project hosted in `eu-central-1` (AWS Frankfurt) for GDPR
data-residency.
**Rationale:**
- Postgres gives us SQL flexibility (Row-Level Security, PostGIS for geo
  queries, native foreign keys for trip/member/item relationships).
- Realtime, Auth (Apple + Google providers), Storage, and Edge Functions
  are all first-party — no provider sprawl.
- Per-operation cost is materially lower than Firebase Firestore at
  100K+ MAU.
**Rejected alternatives:**
- Firebase — simpler `onDisconnect()` presence pattern, but Firestore
  pricing at scale and weaker SQL story are deal-breakers for our
  multi-trip / multi-member query patterns.
- Appwrite — viable on price but smaller community and weaker realtime story.
- Convex — beautiful DX but pre-IPO vendor risk and no SQL escape hatch.
**Swap cost if we change later:** **High** — touches data model, RLS
policies, Edge Functions, and auth. We mitigate with Drizzle ORM (Postgres
schema we own) + thin DAO layer.
**Linked research:** `docs/research/tech-landscape.md` §6 + App. F.
**Constraints baked in:**
- Apple Sign-In is App-Store-mandatory whenever any third-party social
  sign-in is offered.
- SQLCipher-encrypted local SQLite is available via op-sqlite.

### 4. Real-time collaboration / sync (three roles, three components)
**Chosen:**
- **Server-of-record sync (Postgres ↔ local SQLite):** **PowerSync**
  ($49/mo Pro from day one to skip building a custom CDC sync layer).
- **CRDT document state for live multi-user editing of trip items / notes:**
  **Yjs** (MIT) running over **y-websocket** hosted on **Fly.io**.
- **Presence + cursors + "who's looking at this item":** **Supabase
  Realtime Presence** channels (native broadcast + presence semantics).
**Rationale:**
- Three different consistency models, three right tools. Postgres holds the
  durable trip record (PowerSync syncs it). Yjs handles concurrent edits
  to free-form structures (notes, ordered item lists) without manual
  merge UI. Supabase Presence handles ephemeral state that doesn't need
  persistence.
- PowerSync at $49/mo is materially cheaper than 2–3 weeks of one engineer
  building and maintaining a custom CDC-based sync layer.
- Yjs has the largest CRDT ecosystem (y-indexeddb, y-websocket,
  Liveblocks native support) — see `tech-landscape.md` trade-offs.
**Rejected alternatives:**
- Pure Supabase Realtime for both sync and live editing — fine for the
  former, weak conflict story for the latter.
- Liveblocks — managed Yjs is delightful but expensive at scale; we
  self-host y-websocket on Fly.io for ~$5–50/mo.
- Automerge — equally correct, smaller ecosystem.
**Swap cost if we change later:** **Medium** for PowerSync (rewrite the
sync worker against Postgres CDC directly). **Low** for the y-websocket
provider (Yjs is provider-agnostic). **Low** for Supabase Presence (small
surface).
**Linked research:** `docs/research/tech-landscape.md` App. B (data flow)
+ trade-offs.
**Gates:** Spikes B (live multi-user edit) and D (offline reconnect)
validate this stack end-to-end.

### 5. Local persistence / offline-first
**Chosen:** **op-sqlite** with **Drizzle ORM** for schema + queries, plus a
`sync_status` column on every row managed by PowerSync. Yjs document state
persisted in a sidecar table.
**Rationale:**
- op-sqlite is the fastest RN SQLite binding (JSI, no bridge crossings)
  and supports SQLCipher encryption for offline data security.
- Drizzle gives us TypeScript types end-to-end and Postgres-compatible
  schema we can share with the Supabase side.
**Rejected alternatives:**
- WatermelonDB — battle-tested but its sync model fights PowerSync's.
- MongoDB Realm / Atlas Device SDK — **deprecated September 2024**,
  EOL September 2025. Hard pass.
- Expo SQLite — simpler but slower and no SQLCipher.
**Swap cost:** **Low–Medium** — Drizzle is portable; swapping the SQLite
binding is a few hundred lines.
**Linked research:** `docs/research/tech-landscape.md` §7 + Realm
deprecation citation.

### 6. Link / URL extraction service
**Chosen:** **Supabase Edge Function (Deno)** running a tiered pipeline:
1. Open Graph + JSON-LD (schema.org Hotel, LodgingBusiness, Event, etc.)
2. oEmbed (TikTok, YouTube)
3. Mozilla Readability extraction
4. **OpenAI GPT-4o-mini** for structured JSON extraction with a
   `response_format: json_object` constraint and ~3K-token snippet cap.
**Rationale:**
- Lives next to our auth + RLS — no separate service to deploy or secure.
- Free / very cheap tiers 1–3 handle the majority of hotel + restaurant
  + Airbnb URLs without paying for LLM tokens.
- GPT-4o-mini is the current price/quality leader for ~500-token
  extractions.
**Rejected alternatives:**
- Cloudflare Workers / Vercel Edge — fine, but adds a separate provider
  and a separate secrets store.
- LLM-only path — wasteful when structured data is already in the page.
**Swap cost:** **Low** — single function with a clear interface.
**Linked research:** `docs/research/tech-landscape.md` App. C (pseudocode).
**Future:** On-device fallback (Apple Foundation Models on iOS 18+,
Gemini Nano on Android) for private / offline link parsing — post-MVP.

### 7. Spreadsheet ingestion
**Chosen:** **SheetJS Community Edition (`xlsx`)** on-device for parsing
the canonical template + user-formatted sheets. Column mapping uses a
two-step pipeline:
1. **Exact + fuzzy header match** against a curated alias dictionary
   (covers ~80% of imports).
2. **LLM column inference** via the same Supabase Edge Function from
   layer 6 (GPT-4o-mini) when match confidence is < 50%.
A preview / commit step always lets the user fix the inferred mapping
before the trip is materialized.
**Rationale:**
- SheetJS supports `.xlsx`, `.xls`, `.csv`, `.tsv`, `.ods`, Numbers, etc.
  — covers every real-world spreadsheet shape.
- On-device parsing keeps private trip data on the device when the
  template matches; LLM fallback only invoked when needed.
**Rejected alternatives:**
- exceljs — Node-first, weaker RN/Expo story.
- Server-side parsing — bigger files, less private, no offline path.
**Swap cost:** **Low** — parsing is a leaf component.
**Linked research:** `docs/research/tech-landscape.md` §5 + App. D
(pseudocode).
**Gate:** Spike E validates 10 user-formatted sheets through the
column-inference fallback.

### 8. Authentication
**Chosen:** **Supabase Auth** with Email + Password, Google, and Apple
providers. Magic-link as a fallback for users without social accounts.
**Constraints:**
- Apple Sign-In is **App Store mandatory** for any app that offers any
  third-party social sign-in.
- Supabase anon key is shipped in the mobile bundle (public by design) —
  all data access is gated by Row-Level Security policies. No private
  keys in the app.
**Swap cost:** **Medium** — tied to Supabase choice.

### 9. File / media storage
**Chosen:** **Supabase Storage** for trip attachments (notes, photos,
PDFs), with signed URLs and RLS-gated access. **Cloudflare R2** in front
as a CDN for read-heavy paths.
**Rationale:**
- R2 has zero egress fees, so serving frequently-viewed attachments is
  effectively free.
- Supabase Storage handles auth and RLS uniformly with the rest of the
  data model.
**Swap cost:** **Low–Medium** — adapter pattern around object storage.

### 10. Push notifications
**Chosen:** **Expo Push Service** (which fans out to APNs + FCM), with
push tokens stored in Supabase and dispatched from Supabase Edge Functions
on collaboration events (member joined, item changed, comment added).
**Rationale:**
- Expo Push handles APNs token rotation, FCM, and silent vs. visible
  semantics in one API.
- No need to handle APNs cert provisioning in CI.
**Swap cost:** **Low** — small dispatch surface; we can call APNs/FCM
directly if Expo's free tier becomes a concern.

### 11. Analytics + crash + feature flags
**Chosen:** **PostHog** for product analytics + feature flags, **Sentry**
for crash + performance + release health, **Statsig** deferred until we
need server-side experiments (post-MVP).
**Rationale:**
- PostHog covers analytics + flags in one tool, with a generous free tier
  and self-hostable later if needed.
- Sentry's React Native SDK is best-in-class for crash + source-map
  uploads via EAS hooks.
**Swap cost:** **Low** — analytics is event-driven, swappable.

### 12. Geocoding & POI search
**Chosen:** **Mapbox Search Box API** (primary) + **Google Places API
(Place Details)** (fallback). Session-token billing keeps Google Places
cheap.
**Rationale:**
- Mapbox autocomplete is good and free up to the same 25K MAU bracket.
- Google Places fills the data-quality gap in SE Asia, Africa, and
  parts of LATAM where OSM-derived data is thin.
- Both go through a single `LocationService` interface in the app.
**Swap cost:** **Low** — single service interface.

### 13. CI/CD for mobile
**Chosen:** **EAS Build + EAS Submit** for the mobile app, **GitHub
Actions** for backend / Edge Functions / Drizzle migrations.
**Rationale:**
- EAS handles iOS signing, OTA updates, and store submission from CI —
  the biggest historical pain points of mobile CI vanish.
- GitHub Actions is already attached to this repository.
**Swap cost:** **Low–Medium** — EAS config is portable to Codemagic or
self-hosted runners if needed.

### 14. Monitoring / observability
**Chosen:** **Sentry** (crash + perf + release health) + **Supabase
built-in logs and metrics** (Postgres slow queries, Realtime channel
stats, Edge Function logs). **Better Stack** or **Axiom** added later if
log-volume justifies a dedicated tool.
**Swap cost:** **Low** — log shipping is straightforward.

### 15. Payments (post-MVP gating)
**Chosen:** **RevenueCat** in front of App Store + Play Billing, with a
Supabase webhook receiver to update entitlements.
**Rationale:**
- Standard for cross-platform IAP; saves us from re-implementing receipt
  validation on both stores.
- The `risk-monetization` decision can land late without forcing
  re-architecture because RevenueCat is wired in but inert until we
  enable a paywall.
**Swap cost:** **Low** — RevenueCat is a thin client SDK + webhook.

---

## One-page cheat sheet

| Layer                     | Picked                                                  | One-liner reason                                                                  |
|---------------------------|---------------------------------------------------------|-----------------------------------------------------------------------------------|
| Mobile framework          | React Native + Expo (New Architecture)                  | Largest hiring pool; JS-first realtime ecosystem; Expo SDK + EAS solves CI pain   |
| Map provider              | Mapbox (renderer + search) + Google Places (fallback)   | Free to 25K MAU; MapLibre is the documented at-scale escape hatch                 |
| Backend / BaaS            | Supabase (eu-central-1 for GDPR)                        | Postgres + RLS + Realtime + Storage + Edge Functions; cheaper than Firestore at scale |
| Realtime / sync           | PowerSync (Postgres↔SQLite) + Yjs / y-websocket (CRDT) + Supabase Presence | Three consistency models, three right tools                          |
| Local persistence         | op-sqlite + Drizzle ORM (SQLCipher on)                  | Fastest RN SQLite; shared schema with Postgres; Realm SDK is EOL Sep 2025         |
| Link extraction           | Supabase Edge Function, tiered (OG/JSON-LD → oEmbed → Readability → GPT-4o-mini) | Cheap-first; LLM only when structured data is absent              |
| Spreadsheet ingestion     | SheetJS on-device + LLM column-inference fallback       | Private by default; LLM only for off-template sheets                              |
| Auth                      | Supabase Auth (Email + Google + Apple + magic link)     | Apple required by App Store; RLS gates all data                                   |
| File / media storage      | Supabase Storage + Cloudflare R2 CDN                    | R2 zero-egress; uniform auth with rest of stack                                   |
| Push                      | Expo Push (→ APNs + FCM) dispatched from Edge Functions | One API for both stores; no APNs cert wrangling                                   |
| Analytics + crash + flags | PostHog + Sentry                                        | Free tiers cover MVP; self-host PostHog later                                     |
| Geocoding / POI           | Mapbox Search Box + Google Places (SE Asia/Africa/LATAM fallback) | Session-token pricing makes Google Places cheap                         |
| CI/CD                     | EAS Build + EAS Submit (mobile) + GitHub Actions (backend) | iOS signing + OTA + submission, all from CI                                    |
| Monitoring                | Sentry + Supabase built-in logs                         | Crash + perf + Postgres slow queries in two surfaces                              |
| Payments (post-MVP)       | RevenueCat                                              | Standard for cross-platform IAP; wired-in-but-inert until a paywall ships         |

---

## Decisions log

| Date     | Layer                  | Decision                                                   | Note |
|----------|------------------------|------------------------------------------------------------|------|
| May 2026 | Mobile framework       | React Native + Expo                                        | See `docs/research/framework-recommendation.md`. Spike A gates revisit. |
| May 2026 | Map provider           | Mapbox primary, Google Places fallback                     | MapLibre + Stadia is the escape hatch above ~100K MAU.                  |
| May 2026 | Backend / BaaS         | Supabase, eu-central-1                                     | Drizzle ORM owns the schema; Realm SDK rejected (EOL Sep 2025).         |
| May 2026 | Realtime / sync        | PowerSync + Yjs/y-websocket + Supabase Presence            | Three components, three roles. Spikes B + D validate end-to-end.        |
| May 2026 | Local persistence      | op-sqlite + Drizzle (SQLCipher)                            | —                                                                       |
| May 2026 | Link extraction        | Supabase Edge Function, tiered, GPT-4o-mini terminal stage | See `tech-landscape.md` App. C.                                         |
| May 2026 | Spreadsheet ingestion  | SheetJS on-device + LLM fallback                           | Spike E validates 10 user-formatted sheets.                             |
| May 2026 | Auth                   | Supabase Auth: Email + Google + Apple + magic link         | Apple Sign-In App-Store-mandatory.                                      |
| May 2026 | Storage                | Supabase Storage + R2 CDN                                  | R2 chosen for zero egress on hot attachments.                           |
| May 2026 | Push                   | Expo Push                                                  | Edge Function dispatches on collab events.                              |
| May 2026 | Analytics + flags      | PostHog + Sentry                                           | Statsig deferred until server-side experimentation is needed.           |
| May 2026 | Geocoding / POI        | Mapbox Search Box + Google Places fallback                 | Session-token billing keeps Places cheap.                               |
| May 2026 | CI/CD                  | EAS Build + Submit + GitHub Actions                        | —                                                                       |
| May 2026 | Monitoring             | Sentry + Supabase built-in                                 | Add Better Stack / Axiom later if log volume justifies it.              |
| May 2026 | Payments (post-MVP)    | RevenueCat                                                 | Wired in but inert until `risk-monetization` lands.                     |

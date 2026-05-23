# GoTravel Stack Decision (Draft v0.1 — pre-research skeleton)

> Status: **Skeleton** — the decision criteria, decision template, and
> placeholders are in place. Concrete recommendations will be filled in once
> Phase 1 research returns (`docs/research/tech-landscape.md` and
> `docs/research/framework-recommendation.md`).

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

**Version:** 0.1 (skeleton) · **Last updated:** TBD post-research

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
**Chosen:** **TBD** (default expectation: React Native + Expo)
**Linked research:** `docs/research/framework-recommendation.md`
**Open questions:**
- Confirm whether Mapbox / Google Maps native bindings on Flutter have
  closed their feature gap with iOS native by 2025–2026.
- Confirm Storybook / component-library tooling status on the chosen
  framework.

### 2. Map provider
**Chosen:** **TBD** (default expectation: Google Maps Platform, with a
provider-abstraction layer so Mapbox / MapLibre is a swap, not a rewrite)
**Linked research:** `docs/research/tech-landscape.md` §1
**Open questions:**
- Cost modeling at 10K / 100K / 1M MAU.
- Does our map-heavy itinerary use case hit any rate limits we'd notice?

### 3. Backend / BaaS
**Chosen:** **TBD** (default expectation: **Supabase** for the lean-MVP
profile; **Firebase** if Phase 1 research favors it specifically for the
realtime + push combination)
**Linked research:** `docs/research/tech-landscape.md` §6
**Open questions:**
- Auth providers required for App Store compliance (Apple sign-in is
  mandatory on iOS).
- Storage egress costs for trip attachments.
- Postgres extension availability (PostGIS for geo queries).

### 4. Real-time collaboration / sync
**Chosen:** **TBD** (default expectation: BaaS-native realtime first;
graduate to a CRDT layer (Y.js / Automerge) wrapping the BaaS only if Spike B
proves BaaS-native presence + offline merge isn't acceptable)
**Linked research:** `docs/research/tech-landscape.md` §3
**Open questions:**
- Can we get sub-200ms presence updates over the BaaS realtime channel
  globally? Or do we need a separate WebSocket layer for presence?

### 5. Local persistence / offline-first
**Chosen:** **TBD** (default expectation: SQLite via the chosen framework's
preferred wrapper — `op-sqlite` (RN) or `sqflite` (Flutter) — with a
small change-journal table and a sync worker on app foregrounding)
**Linked research:** `docs/research/tech-landscape.md` §7

### 6. Link / URL extraction service
**Chosen:** **Edge worker** (Cloudflare Workers or Vercel Edge) running a
tiered pipeline:
1. Open Graph + Twitter Card + JSON-LD (no LLM, near-zero cost)
2. oEmbed providers
3. Readability extraction
4. LLM extraction (model TBD)

**Linked research:** `docs/research/tech-landscape.md` §4
**Open questions:**
- Which LLM is best price/quality for ~500-token extractions?
- On-device fallback (Apple Foundation Models on iOS 18+, Gemini Nano on
  Android) for private/offline cases.

### 7. Spreadsheet ingestion
**Chosen:** **TBD** (default expectation: **SheetJS / xlsx** on-device for
the canonical template; same edge worker as link extraction handles
LLM-assisted column inference for off-template sheets)
**Linked research:** `docs/research/tech-landscape.md` §5

### 8. Authentication
**Chosen:** **TBD** (whichever the chosen BaaS provides; must include
email/password, Sign in with Google, Sign in with Apple).
**Linked research:** `docs/research/tech-landscape.md` §6
**Constraints:** Apple sign-in is **mandatory** on iOS for any app that
offers third-party social sign-in.

### 9. File / media storage
**Chosen:** **TBD** (default expectation: BaaS-native object storage
with signed URLs; CDN in front for trip attachments).

### 10. Push notifications
**Chosen:** **TBD** (default expectation: APNs + FCM via the chosen BaaS;
Expo Push if RN + Expo).

### 11. Analytics + crash + feature flags
**Chosen:** **TBD** (default expectation: PostHog for analytics + flags
(self-hostable later), Sentry for crash + performance).

### 12. Geocoding & POI search
**Chosen:** **TBD** (default expectation: same as the map provider; with a
fallback to Foursquare or OSM Nominatim for places the map provider doesn't
have well).

### 13. CI/CD for mobile
**Chosen:** **TBD** (default expectation: EAS Build + EAS Submit if RN+Expo;
Codemagic if Flutter; Fastlane + GitHub Actions if dual-native).

### 14. Monitoring / observability
**Chosen:** **TBD** (Sentry covers crash + perf; backend observability
TBD based on BaaS choice).

### 15. Payments (post-MVP gating)
**Chosen:** **TBD** (default expectation: RevenueCat in front of App Store
+ Play Billing once we introduce paid tiers).

---

## "One-page cheat sheet" (to fill in after research)

| Layer                    | Picked | One-liner reason |
|--------------------------|--------|------------------|
| Mobile framework         | TBD    |                  |
| Map provider             | TBD    |                  |
| Backend / BaaS           | TBD    |                  |
| Realtime / sync          | TBD    |                  |
| Local persistence        | TBD    |                  |
| Link extraction          | Edge worker, tiered (OG/JSON-LD → LLM) | Cheap-first; falls back only when needed |
| Spreadsheet ingestion    | TBD    |                  |
| Auth                     | TBD    | Email + Google + Apple (Apple required by App Store) |
| File / media storage     | TBD    |                  |
| Push                     | TBD    |                  |
| Analytics + crash + flags| PostHog + Sentry | Free tiers cover MVP; self-host PostHog if needed |
| Geocoding / POI          | TBD    |                  |
| CI/CD                    | TBD    |                  |
| Payments (post-MVP)      | RevenueCat | Standard for cross-platform IAP |

---

## Decisions log

| Date | Layer | Decision | Note |
|------|-------|----------|------|
| TBD  | —     | —        | First decisions land after `docs/research/tech-landscape.md` is complete. |

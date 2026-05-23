# GoTravel — Cross-Platform Framework Recommendation

> Source: Phase 1 research agent (`tech-landscape-research`), May 2026.
> Distilled into a single decision document.

## TL;DR

**Pick: React Native + Expo.**

Reject Flutter for GoTravel's specific workload, and reject dual-native
(SwiftUI + Jetpack Compose) on lean-team economics.

## How we evaluated

Per Plan §4.5, we scored each option on:

1. Developer velocity for one engineer building one feature.
2. Map SDK quality and customization headroom.
3. Real-time / WebSocket / CRDT library availability.
4. Native module ecosystem (camera, file picker, share sheets, deep links).
5. Build & release pipeline (EAS, Codemagic, Fastlane).
6. Performance on map-heavy screens (1000+ markers, polylines).
7. Hiring market.
8. Long-term maintenance risk.

## Scorecard

| Criterion                                          | RN + Expo                         | Flutter                            | Dual-native (SwiftUI + Compose) |
|----------------------------------------------------|-----------------------------------|------------------------------------|----------------------------------|
| Velocity (1 dev, 1 feature)                        | ✅ Single codebase, hot reload    | ✅ Single codebase, hot reload     | ❌ 2× the work                   |
| Map SDK quality (Mapbox / Google)                  | ✅ Mature RN bindings, JSI bridge | ⚠ Catching up; some feature gaps   | ✅ Native first                  |
| Real-time + WebSocket libs                         | ✅ Massive JS ecosystem (Yjs, ws) | ⚠ Smaller Dart ecosystem           | ⚠ Hand-roll per platform         |
| Native modules (camera, file picker, share sheets) | ✅ Expo SDK covers all of these   | ✅ Strong plugin ecosystem         | ✅ Native                        |
| Build & release pipeline                           | ✅ EAS Build + Submit             | ✅ Codemagic                       | ⚠ Fastlane per platform          |
| Map-heavy screen performance                       | ⚠ Marker count needs profiling — heavy work in native Mapbox layer anyway | ✅ Impeller is consistent | ✅ Native ceiling |
| Hiring market                                      | ✅ JS is largest pool             | ⚠ Dart is small                    | ⚠ Per-platform specialists needed |
| Maintenance risk                                   | ✅ Meta + community               | ✅ Google + community               | ⚠ 2 codebases to maintain        |

## Why React Native + Expo wins for GoTravel

1. **Hiring market is decisive.** JavaScript / TypeScript talent is the
   single largest mobile-capable pool. For a lean MVP, the cost of being
   the only company hiring for Dart is real.

2. **The map performance argument for Flutter is weaker than it looks for
   our workload.** The heavy rendering on our primary screen (map + list)
   happens in the native Mapbox layer regardless of framework. Flutter's
   Impeller advantage matters for animation-heavy custom UIs, not for
   map-tile rendering. We mitigate residual risk with **Spike A** (Phase
   5) — 500 markers + polylines + clustering on a mid-tier Android at
   60fps.

3. **Real-time and CRDT ecosystems are JavaScript-first.** Yjs, Automerge,
   y-websocket, Liveblocks SDKs, PowerSync's web/RN SDK, Supabase
   Realtime — all have first-class JS/TS support. Dart bindings are
   either community-maintained or trail by a release.

4. **Expo SDK 50+ covers every native module we need** out of the box
   (camera, file picker, share sheets, deep links, secure storage, push,
   in-app browser, location, splash screen). The historical "you have to
   eject for X" complaint about Expo has largely been resolved.

5. **EAS Build + EAS Submit is a strictly better pipeline than Fastlane**
   for our team size — managed iOS signing, OTA updates, store submission
   from CI.

## Why we reject Flutter

- Smaller Dart hiring pool.
- Smaller real-time / CRDT ecosystem in Dart.
- Map SDK feature gap on certain provider features (custom layers, vector
  styling extensibility) — narrowing but still present in 2025.
- Google Maps + Firebase + Google Pay integration is genuinely seamless,
  but we're not betting on Firebase (see `stack-decision.md`) so this
  advantage is weakened.

## Why we reject dual-native

- 2× engineering capacity needed.
- 2× build pipelines to maintain.
- Cross-platform consistency becomes a manual job rather than a property
  of the framework.
- Reasonable only if we had a 5+ engineer mobile team and a brand reason
  for native fidelity. Neither applies.

## What we will revisit

- Spike A result: if RN can't hit 60fps with 130+ markers on a mid-tier
  Android (Pixel 6a / mid-tier Samsung A-series), we revisit.
- If Apple Vision Pro / spatial computing becomes a real travel-planning
  surface, we'd want to evaluate native SwiftUI specifically for that
  target.

## Decision log

| Date     | Decision                                | Note |
|----------|-----------------------------------------|------|
| May 2026 | React Native + Expo chosen for the MVP. | Based on `docs/research/tech-landscape.md` and the scorecard above. Spike A is the gate that could revisit this. |

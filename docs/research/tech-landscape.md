> Source: Phase 1 research agent (``tech-landscape-research``), May 2026.
> This agent's full body was not delivered in the response (the agent ran
> out of internal context after the citations section). What was returned
> — and is reproduced below — is the **citations list, six appendices, and
> a "key trade-offs" summary**. The appendices are dense and concrete
> enough to drive the stack decision: a decision tree for map providers,
> an offline-first data flow, pseudocode for URL extraction and spreadsheet
> column mapping, a 3-year TCO model, and a security/compliance checklist.
>
> The actionable recommendations have been distilled into:
>   - `docs/research/framework-recommendation.md` (RN + Expo over Flutter)
>   - `docs/architecture/stack-decision.md` (all 15 layers filled in)
>
> If we need a deeper write-up of any layer, the source of truth is this
> file plus the cited primary sources.

---

# GoTravel Tech Landscape Research

## Citations (continued)

[^yjs-docs]: Yjs Documentation. https://docs.yjs.dev/ (accessed July 2025).
Confirms: MIT license; CRDT implementation using YATA algorithm;
network-agnostic (pluggable providers); claims fastest CRDT implementation
benchmarks; Liveblocks, Y-Sweet, and Tiptap listed as managed Yjs hosting
providers.

[^sheetjs-docs]: SheetJS Community Edition Documentation.
https://docs.sheetjs.com/ (accessed July 2025). Confirms: Apache 2.0
license; supports `.xlsx`, `.xls`, `.xlsm`, `.ods`, `.csv`, `.tsv`,
Numbers, Quattro Pro; runs in browsers, Node.js, React Native, and Expo;
demo projects available for mobile targets.

[^realm-deprecation]: MongoDB — Atlas Device SDK Deprecation Notice.
https://realm.io/ (accessed July 2025). Confirms verbatim: "As of
September 2024, Atlas Device SDKs are deprecated. Atlas Device SDKs will
reach end-of-life and be removed on September 30, 2025." Atlas Device Sync
deprecated; community branches (without sync) exist for Swift, Kotlin,
Flutter, .NET, and JavaScript.

[^powersync-pricing]: PowerSync — Pricing. https://powersync.com/pricing
(accessed July 2025). Confirms: Free plan 50 peak concurrent connections,
2GB synced/month; Pro $49/mo (1,000 concurrent connections, 30GB/month,
then $1/GB); Team $599/mo; Free plan projects deactivate after 1 week of
inactivity.

[^appwrite-pricing]: Appwrite — Pricing. https://appwrite.io/pricing
(accessed July 2025). Confirms: Free tier 75K MAU, 2 projects, 5GB
bandwidth; Pro $15/mo per project, 200K MAU included then $3/1K MAU; APNs
and FCM push supported via Appwrite Messaging.

[^statsig-pricing]: Statsig — Pricing FAQ. https://statsig.com/pricing
(accessed July 2025). Confirms: Developer tier 2M metered events/month
free; Pro tier $150/mo base + 5M events, then $0.05/1K overages; metered
events defined as exposures, log events, ingested metrics.

[^here-pricing]: HERE Technologies — Pricing. https://here.com/pricing/
(accessed July 2025). Note: HERE's pricing page returned only a
contact-sales form — no self-serve pricing tiers published. Pricing
requires direct sales engagement.

[^rn-dev]: React Native — Official Website. https://reactnative.dev/
(accessed July 2025). Confirms: Meta-supported; community-driven; New
Architecture (Fabric + JSI) stable; Expo recommended as the framework
layer for new projects.

[^flutter-dev]: Flutter — Official Website. https://flutter.dev/ (accessed
July 2025). Confirms: BSD 3-Clause license; Dart-compiled to ARM / Intel
machine code; Impeller renderer default since Flutter 3.10; seamless
Google Maps + Firebase + Google Pay integration.

[^maplibre-org]: MapLibre — Official Website. https://maplibre.org/
(accessed July 2025). Confirms: BSD 2-Clause license; MapLibre GL Native
is a C++ library for iOS, Android, desktop, and embedded; OpenGL ES,
Metal, and Vulkan rendering backends; MapLibre Style Specification is
compatible with Mapbox GL JS styles; Martin tile server (Rust) also part
of the ecosystem.

[^mapbox-ios-docs]: Mapbox Maps SDK for iOS — Guide.
https://docs.mapbox.com/ios/maps/guides/ (accessed July 2025). Confirms:
iOS 14+ required; Xcode 16 minimum for SDK v11; Swift 5.9+; requires
Mapbox wordmark attribution on all maps; Telemetry opt-out available
through attribution control.

[^mapbox-android-docs]: Mapbox Maps SDK for Android — Guide.
https://docs.mapbox.com/android/maps/guides/ (accessed July 2025).
Confirms: Android SDK API 21+; OpenGL ES 3 required; Kotlin 1.6.0+; NDK 27
supported from v11.7.0 (with 16KB page size support); Java interoperability
confirmed.

---

## Appendix A — Decision Tree: "Which map provider should GoTravel use?"

```
                    START
                      │
          Is your MAU < 25,000?
          ┌─────────┴──────────┐
         YES                  NO
          │                    │
    Use Mapbox           Is budget a primary
    (free tier,          constraint at scale?
    best DX)             ┌────┴────┐
                        YES       NO
                         │         │
               Migrate to       Stay on
               MapLibre +       Mapbox
               Stadia Maps      (MAU pricing
               (BSD + cheap     predictable)
               tile hosting)
```

---

## Appendix B — Offline-First Data Flow Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                   GoTravel Mobile App                         │
│                                                              │
│  User Action (add pin, edit hotel, filter day)               │
│       │                                                      │
│       ▼                                                      │
│  Yjs Y.Doc (CRDT document for the trip)                      │
│       │  ← applies update locally, no network needed         │
│       ▼                                                      │
│  Drizzle ORM → op-sqlite (local SQLite)                      │
│       │  ← persisted to disk immediately                     │
│       │                                                      │
│       ├── [ONLINE] ──────────────────────────────────┐       │
│       │              PowerSync sync engine            │       │
│       │              pushes SQLite delta → Supabase   │       │
│       │              Postgres (server of record)      │       │
│       │                                               │       │
│       │              Yjs update broadcast →           │       │
│       │              y-websocket server (Fly.io)      │       │
│       │              → other connected collaborators  │       │
│       │                                               │       │
│       └── [OFFLINE] ─────────────────────────────────┘       │
│              Changes queue in SQLite sync_status='pending'    │
│              Yjs offline state buffered in Y.Doc              │
│              On reconnect: PowerSync auto-reconciles          │
│              Yjs CRDT merges concurrent offline edits         │
│              No data loss, no manual conflict UI              │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Appendix C — URL Extraction Pipeline: Detailed Pseudocode

```typescript
// server/extractUrl.ts — Supabase Edge Function (Deno)

import { DOMParser } from "jsr:@b-fuze/deno-dom";
import { Readability } from "npm:@mozilla/readability";

const OEMBED_PROVIDERS = {
  "tiktok.com": "https://www.tiktok.com/oembed?url=",
  "youtube.com": "https://www.youtube.com/oembed?url=",
  "youtu.be":   "https://www.youtube.com/oembed?url=",
};

export async function extractFromUrl(url: string): Promise<ItineraryItem> {

  // ── Tier 1: Fetch HTML + parse Open Graph / JSON-LD ─────────────────────
  const html = await fetch(url, {
    headers: { "User-Agent": "GoTravelBot/1.0 (compatible)" }
  }).then(r => r.text());

  const doc = new DOMParser().parseFromString(html, "text/html");
  const og  = parseOpenGraph(doc);          // name, image, description
  const ld  = parseJsonLD(doc);             // schema.org Hotel/LodgingBusiness etc.

  const tier1 = merge(og, ld);
  if (isConfident(tier1)) return canonicalize(tier1); // ≥3 key fields present

  // ── Tier 2: oEmbed ────────────────────────────────────────────────────────
  const domain = new URL(url).hostname.replace("www.", "");
  if (OEMBED_PROVIDERS[domain]) {
    const oembed = await fetch(OEMBED_PROVIDERS[domain] + encodeURIComponent(url))
      .then(r => r.json());
    const tier2 = merge(tier1, mapOembed(oembed));
    if (isConfident(tier2)) return canonicalize(tier2);
  }

  // ── Tier 3: Readability extraction ───────────────────────────────────────
  const reader  = new Readability(doc);
  const article = reader.parse();           // { title, textContent, excerpt }
  const tier3   = merge(tier2 ?? tier1, {
    name: article?.title,
    description: article?.excerpt,
  });

  // ── Tier 4: LLM extraction (GPT-4o-mini) ──────────────────────────────────
  const snippet = (article?.textContent ?? html)
    .substring(0, 12_000);                  // ~3K tokens when trimmed

  const response = await openai.chat.completions.create({
    model: "gpt-4o-mini",
    response_format: { type: "json_object" },
    messages: [
      { role: "system", content: EXTRACTION_SYSTEM_PROMPT },
      { role: "user",   content: `URL: ${url}\n\nContent:\n${snippet}` },
    ],
    max_tokens: 600,
  });

  const llmData = JSON.parse(response.choices[0].message.content);
  return canonicalize(merge(tier3, llmData));
}
```

---

## Appendix D — Spreadsheet Import: Column Mapping with LLM Fallback

```typescript
// Canonical GoTravel column names
const CANONICAL_COLUMNS = [
  "day_number", "date", "activity_name", "location_name",
  "address", "latitude", "longitude", "budget_amount",
  "currency_code", "start_time", "end_time", "notes",
  "category", "url", "skip"
] as const;

// Step 1: Exact + fuzzy header match (covers 80% of imports)
const ALIASES: Record<string, CanonicalColumn> = {
  "day": "day_number", "day #": "day_number", "day number": "day_number",
  "date": "date", "when": "date", "travel date": "date",
  "hotel": "location_name", "accommodation": "location_name",
  "place": "location_name", "destination": "location_name",
  "activity": "activity_name", "what": "activity_name", "todo": "activity_name",
  "cost": "budget_amount", "price": "budget_amount", "budget": "budget_amount",
  "currency": "currency_code", "ccy": "currency_code",
  "lat": "latitude", "lng": "longitude", "lon": "longitude",
  "note": "notes", "comment": "notes", "details": "notes",
};

function exactMatch(headers: string[]): ColumnMap {
  return headers.reduce((map, header, idx) => {
    const normalized = header.trim().toLowerCase();
    const canonical  = ALIASES[normalized] ?? null;
    if (canonical) map[idx] = canonical;
    return map;
  }, {} as ColumnMap);
}

// Step 2: LLM inference when confidence < 50% matched columns
async function llmColumnInference(
  headers: string[],
  sampleRows: string[][]
): Promise<ColumnMap> {

  const tsv = [headers, ...sampleRows.slice(0, 2)]
    .map(row => row.join("\t"))
    .join("\n");

  const response = await openai.chat.completions.create({
    model: "gpt-4o-mini",
    response_format: { type: "json_object" },
    messages: [{
      role: "system",
      content: `You are a column mapper for a travel itinerary app.
Given the spreadsheet header row and 2 sample data rows below,
map each column INDEX (0-based) to one of these canonical field names:
${CANONICAL_COLUMNS.join(", ")}

Only include columns you are ≥80% confident about.
Return JSON: { "0": "field_name", "1": "field_name", ... }`
    }, {
      role: "user",
      content: tsv
    }],
    max_tokens: 300,
  });

  return JSON.parse(response.choices[0].message.content);
}
```

---

## Appendix E — Estimated 3-Year Total Cost of Ownership

Scenario assumptions: GoTravel launches with 2 engineers, grows from 1K to
500K MAU over 3 years. Usage scales proportionally.

| Cost center          | Year 1 (avg 5K MAU) | Year 2 (avg 50K MAU) | Year 3 (avg 300K MAU)  |
|----------------------|---------------------|----------------------|------------------------|
| Supabase             | $0 (free)           | $25–$44/mo           | $200–$500/mo           |
| PowerSync            | $0 (free)           | $49/mo               | $299–$599/mo           |
| Mapbox / MapLibre    | $0 (≤25K free)      | $300–$500/mo         | $800–$1,500/mo         |
| y-websocket (Fly.io) | $0–$5/mo            | $5–$20/mo            | $20–$50/mo             |
| LLM extraction       | $0–$5/mo            | $25–$75/mo           | $150–$300/mo           |
| EAS Build            | $0 (free tier)      | $99/mo               | $99/mo                 |
| PostHog              | $0 (free)           | $50–$200/mo          | $500–$1,000/mo         |
| Sentry               | $0 (dev)            | $26/mo               | $26–$80/mo             |
| Storage/CDN (R2)     | $0                  | $5–$10/mo            | $30–$80/mo             |
| **Monthly total**    | **~$0–$10**         | **~$560–$1,020**     | **~$2,100–$4,200**     |
| **Annual total**     | **~$60–$120**       | **~$6,700–$12,240**  | **~$25,200–$50,400**   |

> **Key insight:** GoTravel can be built and operated at effectively zero
> infrastructure cost through the first 25,000 MAU, validating
> product-market fit before incurring meaningful SaaS spend. The
> $25K–$50K/year range at 300K MAU is well within range for a funded
> travel-tech startup generating revenue from a SaaS subscription or
> booking commission model.

---

## Appendix F — Security & Compliance Checklist

Given GoTravel's global audience and multi-currency financial data:

| Requirement                                                  | Implementation                                                                                  | Provider       |
|--------------------------------------------------------------|-------------------------------------------------------------------------------------------------|----------------|
| **Apple Sign-In** (App Store mandatory for social login)     | Supabase Auth Apple provider                                                                    | Supabase       |
| **Google Sign-In**                                           | Supabase Auth Google provider                                                                   | Supabase       |
| **GDPR compliance**                                          | Supabase (EU region), Stadia Maps (GDPR-first)                                                  | Supabase, Stadia |
| **Data residency**                                           | Supabase project hosted in `eu-central-1` (AWS Frankfurt)                                       | Supabase       |
| **In-transit encryption**                                    | TLS 1.3 enforced on all API calls                                                               | Default        |
| **At-rest encryption**                                       | Supabase Postgres encrypted at rest (AES-256)                                                   | Supabase       |
| **Row-level security**                                       | Supabase RLS policies (trip access = collaborator list)                                         | Supabase       |
| **API key security**                                         | All LLM/map API keys server-side only (Edge Functions)                                          | Supabase Edge  |
| **No secrets in mobile bundle**                              | Supabase anon key (public by design) + RLS; no private keys in app                              | Architecture   |
| **Currency/financial data**                                  | ISO 4217 currency codes stored in Postgres; formatting via `Intl.NumberFormat`                  | Standard       |
| **App Transport Security (iOS)**                             | All endpoints HTTPS; `NSAllowsArbitraryLoads = false`                                           | Default in Expo|
| **Certificate pinning**                                      | Not required for BaaS (use standard TLS); add for payment flows                                 | Deferred       |
| **Push token rotation**                                      | Expo Push Service handles APNs token refresh automatically                                      | Expo           |
| **Offline data security**                                    | SQLite encrypted via SQLCipher (available via `op-sqlite` with encryption flag)                 | op-sqlite      |

---

## Summary of Key Trade-offs and Dissenting Views

The technology landscape contains genuine disagreements among
practitioners. GoTravel's team should be aware of the most significant ones
before finalizing decisions.

### React Native vs. Flutter

The single largest debate in cross-platform mobile in 2025. Flutter
advocates argue that Dart compilation to ARM machine code and Impeller's
consistent rendering give better frame-rate predictability on low-end
Android devices than RN's JS runtime. This is a legitimate argument for
**animation-heavy UIs** but less relevant for GoTravel's primary screen
(map + list), where the heavy rendering (map tiles) runs in the native
Mapbox layer regardless of framework. The hiring market argument
(JS >> Dart) is decisive for a lean team.

### Supabase Realtime vs. Firebase Realtime Database for Presence

Firebase RTDB's `onDisconnect()` presence pattern is battle-tested and
simpler to implement than Supabase Realtime Presence. If GoTravel's team
has existing Firebase expertise, the Firebase path is lower-risk for the
MVP presence feature. The long-term argument for Supabase is SQL
flexibility and lower per-operation cost at scale.

### Yjs vs. Automerge for CRDTs

Both are MIT-licensed, correct CRDT implementations. Yjs is faster and has
a larger ecosystem of provider integrations (y-websocket, y-indexeddb,
Liveblocks native support). Automerge's Rust-compiled Wasm core has better
formal-verification properties and more flexible schema (arbitrary JSON,
not just Yjs shared types). For GoTravel's use case (collaborative
itinerary editing, not rich text), both would work. Yjs is recommended
purely on ecosystem maturity.

### PowerSync vs. Custom Sync Protocol

PowerSync adds $49/month and a vendor dependency. An experienced engineer
could build a custom sync protocol using Supabase Postgres CDC (Change
Data Capture via Realtime) + local Drizzle ORM writes in ~2–3 weeks. The
trade-off is engineering time (expensive) vs. $49/month (cheap). For an
MVP, $49/month to avoid building and maintaining a sync layer is the
correct call. Revisit at 500K+ MAU if PowerSync pricing becomes material.

### Google Maps Places API Data Quality

In Southeast Asia, Africa, and parts of Latin America — high-priority
travel destinations — Google Maps POI data quality (business hours,
phone numbers, reviews) substantially exceeds OpenStreetMap-based
alternatives. For a travel app with a global audience, Google Places API
as a fallback (even if Mapbox Search Box is primary) is worth the
incremental cost. The session-based autocomplete pricing model (one
session token per user search flow ≈ one "session" charge rather than
one-per-keystroke) makes Google Places significantly cheaper than it
appears at first glance.

---

*Document produced July 2025. All URLs were live and content verified at
time of access. Pricing is subject to change; re-verify all figures before
committing to contracts or architectural decisions. This document feeds
into GoTravel Phase 3: Stack Decision and Technical Architecture
Specification.*

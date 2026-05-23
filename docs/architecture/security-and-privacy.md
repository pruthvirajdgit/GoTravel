# GoTravel Security & Privacy

> Status: v1.0 (Phase 4). Source of truth for the stack is
> `stack-decision.md`; this document is the **policy + implementation
> level**.

## Threat model (one paragraph)

GoTravel handles **trip itineraries, addresses, dates of travel,
collaborator identities, photos, and per-item financial data** for users
worldwide. The primary threats are: (1) account takeover leading to
disclosure of travel plans; (2) one trip member exfiltrating another
trip's data through misuse of the API; (3) a lost or stolen device
exposing offline-cached trip data; (4) inappropriate sharing of trip
data with third parties (e.g. analytics over-collection); (5)
non-compliance with GDPR / CCPA / App Store / Play Store privacy rules.

Out of scope for MVP threat-modeling: nation-state actors,
side-channel attacks on Supabase infrastructure, custom App Store
malware.

## Identity & authentication

- **Providers:** Email + password (with magic-link as fallback), Google
  Sign-In, Apple Sign-In.
- **Apple Sign-In is mandatory** on iOS as soon as any third-party
  social sign-in is offered. App Store rejection otherwise.
- **JWTs:** Supabase issues short-lived (1h) access tokens + long-lived
  (7d sliding) refresh tokens. Refresh tokens are stored in
  `expo-secure-store` (Keychain on iOS, EncryptedSharedPreferences on
  Android). Never in `AsyncStorage`.
- **No private API keys in the mobile bundle.** Only the Supabase **anon
  key** ships in the app — it's public by design and is gated by RLS.
- **Session revocation:** the user can sign out of all devices from the
  account screen; this invalidates all refresh tokens server-side.

## Authorization model

All data access is mediated by **Postgres Row-Level Security** policies.
We have one and only one rule for membership-gated data:

```sql
-- Helper: which trips can the current user see?
create or replace function auth.trip_ids_for_user()
returns setof text language sql security definer as $$
  select trip_id
  from public.trip_members
  where user_id = auth.uid()
    and removed_at is null;
$$;

-- Apply to every membership-gated table
alter table public.trips        enable row level security;
alter table public.trip_members enable row level security;
alter table public.days         enable row level security;
alter table public.items        enable row level security;
alter table public.locations    enable row level security;
alter table public.costs        enable row level security;
alter table public.attachments  enable row level security;
alter table public.comments     enable row level security;

create policy "members can see trip rows"
  on public.items for select
  using ( trip_id in (select auth.trip_ids_for_user()) );

create policy "editors+owners can mutate trip rows"
  on public.items for insert with check (
    trip_id in (
      select trip_id from public.trip_members
      where user_id = auth.uid() and role in ('owner','editor')
        and removed_at is null
    )
  );
-- (analogous update/delete policies)
```

Repeated for every membership-gated table. Viewers cannot mutate.
Removed members cannot read.

## Invite links and shareable links

- **Invite link** = `https://gotravel.app/i/<token>`. Token is signed by
  a Supabase Edge Function using HMAC-SHA256 with a server-side secret;
  payload is `{ tripId, role, exp, jti }`. Single-use enforced via a
  `consumed_invites` table.
- **Default expiration:** 7 days. Owner can override (1 day / 7 days /
  30 days / no expiration).
- **Read-only share link** = same shape with `role: 'viewer'` and a
  `public: true` flag; this issues a tighter-scoped JWT bound only to
  the trip and only for reads.
- **Revocation:** owners can revoke any active link from a "manage
  links" screen; revocation deletes the row from `invites` and
  publishes a `revokeRoom` message (see `realtime-protocol.md`).

## Data residency & GDPR

- **Primary region:** Supabase project in `eu-central-1` (AWS Frankfurt).
- **R2 attachments:** Cloudflare R2 with EU jurisdiction selected.
- **GDPR data export** (`Right of Access`): one button in the account
  screen produces a `.zip` containing JSON exports of every entity the
  user is on plus signed URLs (24h-expiry) for their attachments.
  Implemented as a Supabase Edge Function; completes in < 2 minutes for
  a typical user.
- **GDPR data deletion** (`Right to Erasure`): one button in the account
  screen. Hard-deletes the user's row + nullifies their `user_id` on
  comments and audit entries (preserving the comment text under a
  "deleted user" label so trip history doesn't break for co-travelers).
  All authored content the user owned-solo (trips with no other
  members) is hard-deleted within 30 days.
- **Subprocessor list** is maintained in the privacy policy and updated
  when a new BaaS is added. Current list: Supabase, PowerSync,
  Cloudflare R2, Mapbox, Google (Places), OpenAI (link/spreadsheet
  extraction), Expo (push), Sentry, PostHog.
- **Data processing addenda** are signed with each subprocessor that
  processes EU personal data.

## Privacy by design

- **No analytics on trip content.** Sentry sends crash + error context
  but with `beforeSend` scrubbing of any field that could contain trip
  data (item names, notes, addresses, comments, attachment URLs).
- **PostHog events are schema'd** — only event names and bounded
  enums; never free-form trip content.
- **LLM extraction calls** scrub the request snippet to its first 12K
  characters (already in the pipeline) and we set `store: false` on
  OpenAI calls when the OpenAI API supports it.
- **No third-party SDKs that fingerprint users.** Specifically not
  Facebook SDK, not TikTok SDK, not any tracking SDK. Push uses Expo
  Push Service (we already trust Expo for builds).

## iOS-specific

- **App Tracking Transparency (ATT)** — the app does **not** track users
  across apps and websites, so we present the ATT prompt only if we
  later add an SDK that does. Today: no prompt needed; declared as such
  in App Privacy.
- **App Privacy nutrition labels:** Data Linked to You = identifiers,
  user content, contact info, location (city-precision); Data Used to
  Track You = none. To be revisited if monetization adds attribution.
- **Permissions:** Camera (attachments), Photo Library (attachments),
  Location When In Use (map centering, current-location pin), Push
  Notifications (opt-in), Contacts (post-MVP for invite suggestions —
  not in MVP).

## Android-specific

- **Android 14+ permissions:** photo picker uses the new
  partial-photo-access API; notifications require explicit runtime opt-in.
- **Foreground service** is **not** used in the MVP. The
  planning-to-tracking handoff (post-MVP) will use a foreground service
  for GPS during a trip, with a persistent notification per Play Store
  policy.
- **Data Safety section** in the Play Console: mirror the iOS labels.

## On-device data security

- **SQLite encrypted at rest** via SQLCipher (op-sqlite supports a
  `encryptionKey` parameter); key derived per-install from
  `expo-secure-store` and never persisted in plain text.
- **Yjs state blob** lives inside the encrypted SQLite database; no
  separate plaintext storage.
- **Attachments cache:** stored in the app's sandboxed Documents
  directory; cleared on sign-out.
- **On sign-out:** the entire SQLite database is deleted, the secure
  store is wiped, and the Sentry / PostHog identifiers are reset.

## In-transit & at-rest

- **TLS 1.3** enforced on all endpoints (Supabase, PowerSync,
  y-websocket, Edge Functions, Mapbox, Google APIs). iOS App Transport
  Security in default-strict mode (`NSAllowsArbitraryLoads = false`).
- **At rest:** Supabase Postgres AES-256 (Supabase default); R2 server-
  side encryption; local SQLite SQLCipher AES-256.
- **Certificate pinning:** not required for BaaS endpoints; revisit
  before payments ship (RevenueCat handles its own pinning).

## Logging & retention

| Source                       | What                                | Retention                            |
|------------------------------|-------------------------------------|--------------------------------------|
| Supabase Postgres            | Domain rows                         | Forever, until user deletes          |
| Supabase Auth audit log      | Sign-ins, password changes          | 90 days                              |
| Supabase Edge Function logs  | Server-side function execution      | 14 days                              |
| Sentry                       | Crashes + perf transactions         | 30 days                              |
| PostHog                      | Product events (schema'd only)      | 12 months                            |
| Cloudflare R2 access logs    | Object reads/writes                 | 14 days                              |
| Mapbox / Google              | Map / search calls (their retention)| Per provider DPAs                    |

## Compliance checklist (pre-launch)

- [ ] Privacy policy published at `gotravel.app/privacy`, linked in App
      Store + Play Store listings, and reachable from the app's settings.
- [ ] Terms of Service published at `gotravel.app/terms`.
- [ ] Subprocessor list published and kept current.
- [ ] App Privacy / Data Safety labels submitted with first build.
- [ ] DPA in place with each subprocessor handling EU personal data.
- [ ] Cookie banner — N/A (mobile app, no web in MVP).
- [ ] Children: app is rated 12+ on iOS / Teen on Play; we do not
      knowingly collect data from users under 13.
- [ ] Apple Sign-In wired and tested on a physical iOS device.
- [ ] "Free Forever" commitment published in the App Store / Play Store
      listings (per gap-analysis Gap 10).
- [ ] Trademark search for "GoTravel" completed (tracked under
      `risk-trademark`).

## Open items still parked

- **Monetization model** (`risk-monetization`) — payments add PCI scope
  and may require an additional DPA.
- **Trademark clearance** (`risk-trademark`) — App Store / Play Store /
  USPTO / EUIPO.
- **Insurance** — directors' & errors-and-omissions before public launch.

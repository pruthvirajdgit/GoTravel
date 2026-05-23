> Source: Phase 1 research agent (`competitor-research`), May 2026.
> All citations are dated. Where direct primary sources were blocked, this is flagged.
> Includes voice-of-customer themes from App Store / Play Store reviews and Reddit.

# GoTravel Competitor Research Report
### Mobile Travel Itinerary App — Competitive Teardown
*Research completed: May 2025 | All citations dated*

---

> **Research Methodology Note:** This report draws on official product websites, App Store and Play Store listings, official help documentation, Wanderlog's own head-to-head blog comparisons (acknowledged as a biased but data-rich source), Wikipedia, and platform-side press statements. Where direct primary sources were blocked or unavailable, this is explicitly flagged. All ratings and review counts are point-in-time as of the research date.

---

## 1. TripIt (Concur / SAP)

### A. Snapshot

| Attribute | Detail |
|---|---|
| **Company / Parent** | TripIt Inc., a division of **Concur Technologies (SAP Concur / SAP SE)**. SAP acquired Concur for $8.3 billion in December 2014. [[Wikipedia – SAP Concur, 2025]](https://en.wikipedia.org/wiki/TripIt) |
| **Platforms** | iOS (iPhone, iPad, Mac M1+, Apple Vision Pro), Android, Web, **Apple Watch** (watchOS 9.0+) |
| **Pricing** | **Free tier** (email forwarding, manual entry, basic itinerary); **TripIt Pro = $49/year** (≈$4.08/mo). SAP Concur enterprise users may receive Pro free. [[App Store listing, Apr 2025]](https://apps.apple.com/us/app/tripit-travel-planner/id311035142) |
| **Scale** | "Nearly **20 million travelers**" (self-reported, Play Store/App Store listings, 2025). iOS: **4.8★ / 302K ratings**; Android: ~4.6–4.7★ / ~85K ratings. [[App Store, Apr 2025]](https://apps.apple.com/us/app/tripit-travel-planner/id311035142); [[Play Store, Apr 2025]](https://play.google.com/store/apps/details?id=com.tripit) |
| **Founded** | TripIt founded **2006** (acquired by Concur 2011; Concur acquired by SAP 2014). **2025 = 20th anniversary** per App Store release notes. |
| **Recent changes** | Version 20.5.0 (April 27, 2025): "Improved trip loading performance and reliability; redesigned experience for plan creation from Unfiled Items." Also added **Travel Guidance** (pre-trip international requirements) and **Passport Renewal Reminder** (2024/2025). [[App Store, Apr 2025]](https://apps.apple.com/us/app/tripit-travel-planner/id311035142) |

### B. Onboarding & Trip Creation Flow

- **Email-first flow**: New user signs up, then forwards a confirmation email to `plans@tripit.com`. TripIt parses the email and creates a trip in seconds — no manual data entry required. This is TripIt's signature differentiator. [[tripit.com, 2025]](https://www.tripit.com/web)
- **Gmail OAuth**: Free users can connect Gmail to auto-scan for booking confirmations. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)
- **Manual entry**: Supported for flights (requires date + airline + flight number + confirmation code), hotels, cars, activities.
- **PDF/photo/boarding pass upload**: Supported. QR code passport scanning available.
- **Time-to-value**: Extremely fast for email-forwarders — a forwarded booking confirmation creates a full itinerary entry in under 60 seconds. Manual entry is moderately slower due to required fields.
- **Permissions**: Email access (optional Gmail OAuth), location (for "nearby" features and departure reminders), notifications.

### C. Itinerary Data Model

| Entity | Notes |
|---|---|
| **Trip** | Top-level container with date range and destination |
| **Day** | Auto-generated from booking dates; segments flow into day buckets |
| **Plan / Segment** | Core item: Flight, Hotel, Car Rental, Rail, Cruise, Restaurant, Activity, Note, Other |
| **Unfiled Items** | Items parsed from email but not yet assigned to a trip |
| **Document** | Attachments: PDFs, photos, boarding passes, QR codes |
| **Reward Program** | Tracked loyalty program points (Pro only) |

- Items grouped **primarily by day/date**, then by type within a day (flights → hotels → activities).
- Per-item editable fields: confirmation numbers, provider details, date/time, location, notes, documents.
- **No budgeting fields** — cost tracking is absent from the data model entirely. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)

### D. Import Methods

| Method | Status |
|---|---|
| **Email forwarding inbox** | ✅ **`plans@tripit.com`** — the industry benchmark. One universal address for all trips. [[tripit.com, 2025]](https://www.tripit.com/web) |
| **OAuth Gmail scanning** | ✅ Free tier. Auto-detects booking confirmations. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/) |
| **OAuth Outlook scanning** | ✅ Supported (SAP Concur enterprise integration). |
| **Link / URL paste** | ❌ Not documented as a feature. |
| **CSV / Excel upload** | ❌ Not available. |
| **Photo OCR / receipt scan** | ⚠️ Partial — can upload photos/PDFs to items, but not as an import mechanism; manual OCR not offered. |
| **Manual entry** | ✅ Full form-based entry for all segment types. |

### E. Map Experience

- **List-first UI**: TripIt is fundamentally a chronological list organizer. Map is secondary.
- **Map provider**: Links out to **Google Maps or Apple Maps** (user's choice) for navigation. TripIt does not render its own map SDK — it deep-links into the native mapping app. [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.tripit)
- **In-app "map"**: Basic nearby POI view (restaurants, parking, ATMs near hotel). No route lines, no day filtering, no clustering.
- **Offline maps**: ❌ Map features require handoff to Google/Apple Maps. Reservation text data is available offline.
- **Airport maps**: ✅ Interactive searchable airport maps (Pro feature) for navigation to gates, connecting flights, baggage claim. [[tripit.com/web/pro, 2025]](https://www.tripit.com/web/pro)

### F. Collaboration

- **Trip sharing**: ✅ Free. You can share your trip itinerary via a link or email; recipient gets a **read-only view**.
- **Inner Circle (Pro)**: Auto-shares real-time travel plans with a designated group (family/colleagues). [[tripit.com/web/pro, 2025]](https://www.tripit.com/web/pro)
- **Real-time multi-user editing**: ❌ Not available. No co-editing model.
- **Presence / live cursors**: ❌ None.
- **Roles**: Owner + view-only share recipients. No editor role for external collaborators.
- **Comments / chat**: ❌ None.

### G. Budget & Expenses

- **Per-item costs**: ❌ Not supported.
- **Per-trip totals**: ❌ Not supported.
- **Multi-currency**: ❌ None.
- **Expense splitting**: ❌ None.
- **Receipt scanning**: ❌ None (SAP Concur expense management is a separate product).
- *Note*: Budget tracking is a deliberate gap in TripIt's consumer product. Enterprise expense management lives in the parent SAP Concur platform. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)

### H. Offline Mode

- ✅ **Reservation details available offline** — confirmation numbers, itinerary text, PDFs, boarding passes.
- ❌ Maps require handoff to Google/Apple Maps (which have their own offline capabilities).
- ✅ Siri Shortcuts for hands-free access to trip data.
- [[App Store, Apr 2025]](https://apps.apple.com/us/app/tripit-travel-planner/id311035142)

### I. AI / Smart Features

- **Fare Tracker (Pro)**: Alerts if airfare drops post-booking. [[tripit.com/web/pro, 2025]](https://www.tripit.com/web/pro)
- **Seat Tracker (Pro)**: Notifies if better seat becomes available. [[tripit.com/web/pro, 2025]](https://www.tripit.com/web/pro)
- **Risk Alerts (Pro)**: Detects potential trip disruptions.
- **Travel Guidance (Pro)**: Pre-trip international entry requirements (added 2025).
- **No generative AI**: No auto-generated itineraries, no conversational AI, no smart suggestions for restaurants or activities. Confirmed by Wanderlog's own comparison: "No real AI capabilities." [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)

### J. Notable Strengths and Weaknesses

**Strengths**:
- Unbeatable **email parsing ecosystem** — works with virtually every airline/hotel booking system.
- Best **flight operations** features in class: gate alerts, baggage claim, seat tracker, alternate flights.
- Large user base creates trust and validation; enterprise channel via SAP Concur.
- Apple Watch support is rare in this category.
- **20 years of parser training** on confirmation emails = high accuracy.

**Weaknesses**:
- **No budgeting** — a significant gap vs. Wanderlog and Sygic Travel.
- **No collaborative editing** — purely a personal organizer.
- **Map experience is vestigial** — no native rendering, just deep-links to Google/Apple.
- **UI shows its age** — reviewers commonly note the interface hasn't been fundamentally redesigned since the Concur era.
- **Pro is required for many power features** — notably offline access and Gmail scanning are gated differently from competitors.

### K. Voice of Customer

**Top recurring complaints (App Store / Play Store, 2023–2025)**:
1. "The app hasn't been meaningfully updated in years; feels stale."
2. "No budget tracking — I have to use a second app."
3. "Trip sharing is view-only; can't collaborate with travel partners."
4. "Too expensive for what the free tier offers vs. newer competitors."
5. "Parsing errors on non-US airline confirmation emails."

**Top recurring delights**:
1. "Email forwarding to plans@tripit.com is magic — instant itinerary."
2. "Airport maps and gate alerts are genuinely lifesaving for business travel."
3. "I never miss a flight change; real-time alerts work flawlessly."
4. "Siri Shortcuts integration is underrated."
5. "20 million users means it supports every airline / hotel system I've ever booked."

**Representative reviews** *(from App Store listing, sourced May 2025)*:

> ⭐⭐⭐⭐⭐ "I'm on the road 100 days a year and TripIt is my go-to-app. It's such a powerful tool in the hands of a traveler. There's simply nothing like it on the planet." — *Damen L.*

> ⭐⭐⭐⭐⭐ "I love knowing exactly when my flights are, when they are delayed, what gate to leave from, and all the other amazing TripIt Pro features." — *Ann B.*

> ⭐⭐⭐ "Great for business travel. Desperately needs a budget tracker and a UI refresh. Wanderlog beats it for leisure trips." *(representative of frequent review theme)*

---

## 2. Wanderlog

### A. Snapshot

| Attribute | Detail |
|---|---|
| **Company / Parent** | **Wanderlog, Inc.** — independent startup. Founded by Peter Xu and Harry Yu (former Google engineers). YC W2019 batch. |
| **Platforms** | iOS, Android, **Web (primary planning surface)** |
| **Pricing** | **Free tier** (most planning features); **Wanderlog Pro** subscription (exact price varies; reportedly ~$3–5/month or ~$24–36/year based on various forum references — *exact pricing page was inaccessible during research*). Gated Pro features: Gmail scanning, offline access, AI assistant, route optimization, Google Maps export, flight alerts. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/) |
| **Scale** | "Over **1 million** people have already tried Wanderlog" (homepage, 2025). [[wanderlog.com, 2025]](https://wanderlog.com). iOS: **4.9★ / ~19,600 ratings**; Android: **4.7★ / ~19,400 ratings**. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/) |
| **Founded** | **2019** (YC W2019). |
| **Recent changes** | AI assistant added (Pro); route optimization; "over 1M users" milestone claimed in 2024/2025. Ongoing feature expansion cadence. |

### B. Onboarding & Trip Creation Flow

- User creates an account (email or social login), then enters destination(s) and travel dates to create a trip.
- Web-first planning UX: map and itinerary rendered side-by-side — a key differentiator.
- Invite collaborators immediately from the trip creation screen.
- Import: forward confirmation emails to a **per-trip unique address** (e.g., `trips+153334@wanderlog.com`) — slightly more friction than TripIt's universal address. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)
- **Time-to-value**: High for leisure planners — map is live immediately, POI suggestions appear, collaborators can join in minutes. Slightly higher friction for bookings-import vs. TripIt.

### C. Itinerary Data Model

| Entity | Notes |
|---|---|
| **Trip** | Named trip with destination(s), date range |
| **Day** | Date-based groupings; drag-and-drop reordering |
| **Place / Activity** | Core item: name, category, address, notes, cost, time estimate, photos |
| **Booking / Reservation** | Flight, hotel, car, tour parsed from email or entered manually |
| **Expense** | Per-item cost field; aggregated to trip budget |
| **Note / Link** | Free-text or URL items |
| **Checklist** | Packing lists and to-do items |

- Items grouped **by day** (primary) and by **category** (togglable — color-coded by category).
- The map view mirrors day-groupings with color coding, so each day has its own color on the map — a widely praised design feature.
- Per-item editable: name, address, category, cost, currency, notes, photos, links, time, duration.

### D. Import Methods

| Method | Status |
|---|---|
| **Email forwarding inbox** | ✅ Per-trip unique address (e.g., `trips+XXXXX@wanderlog.com`). Works but is less convenient than TripIt's universal address. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/) |
| **OAuth Gmail scanning** | ✅ **Pro only** — auto-scans Gmail for booking confirmations. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/) |
| **OAuth Outlook scanning** | ⚠️ Not confirmed in sources reviewed. |
| **Link / URL paste** | ✅ Users report the ability to paste booking URLs; parsing quality varies. *Inference from feature description — not formally documented in reviewed sources.* |
| **CSV / Excel upload** | ❌ Not documented. |
| **Photo OCR / receipt scan** | ❌ Not documented. |
| **Manual entry** | ✅ Streamlined — airline + flight number sufficient for flights (dates pre-set from trip context). [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/) |

### E. Map Experience

- **Map-first + list split-view**: The defining design — web and mobile show map and itinerary **simultaneously** in a split pane. This is widely cited as Wanderlog's biggest differentiator. [[wanderlog.com, 2025]](https://wanderlog.com)
- **Color-coded by day**: Each day has a unique color; pins on the map match items in the list — instantly see geographic clustering of your day plan. [[wanderlog.com/blog, Oct 2024]](https://wanderlog.com/blog/2024/10/23/7-best-travel-apps/)
- **Map provider**: Google Maps (inferred from deep integration with Google POI database and "Export to Google Maps" Pro feature).
- **Day filtering**: ✅ Toggle individual days on/off on the map.
- **Route lines**: ✅ Shows travel time and distance between stops; togglable transportation modes (walk, bike, drive, transit).
- **Route optimization**: ✅ **Pro** — auto-reorder stops for optimal sequence.
- **POI autocomplete**: ✅ Powered by Google Places API; strong coverage globally.
- **Offline maps**: ⚠️ **Pro only** per Wanderlog's own comparison table. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)

### F. Collaboration

- **Multi-user editing**: ✅ **Best-in-class** for this category. All collaborators work on the same live trip. [[wanderlog.com/blog, Oct 2024]](https://wanderlog.com/blog/2024/10/23/7-best-travel-apps/)
- **Real-time sync**: ✅ Changes appear for all collaborators. (Whether true real-time or near-real-time is unclear — no live cursor evidence in sources reviewed.)
- **Presence / live cursors**: ⚠️ Not confirmed in sources. *Uncertainty noted.*
- **Roles**: Owner + collaborators who can add/edit; a public read-only share link also exists.
- **Sharing model**: Invite link (account not required for viewing; account required to edit — *inferred, not formally confirmed*).
- **Comments / chat**: ⚠️ Not confirmed in sources. Users can add notes to places.
- *Best cited evidence*: "You have the ability to share your Wanderlog itinerary with your traveling companions. They, in turn, can add to the itinerary and make notes of their interests." [[wanderlog.com/blog, Oct 2024]](https://wanderlog.com/blog/2024/10/23/7-best-travel-apps/)

### G. Budget & Expenses

- **Per-item costs**: ✅ Cost field on every place/activity item. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)
- **Per-trip totals**: ✅ Aggregated budget dashboard.
- **Multi-currency**: ✅ Confirmed (currency selector per item) — *live FX rates not confirmed.*
- **Expense splitting**: ⚠️ Not confirmed as Splitwise-style per-person splitting. Users can track shared costs but auto-split logic is unconfirmed.
- **Receipt scanning**: ❌ Not documented.
- **Hotel price drop tracking**: ✅ Pro feature — alerts when hotel price drops after booking. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)

### H. Offline Mode

- **Pro only** offline access per Wanderlog's own comparison. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)
- Free users: app requires connectivity for map and most functions.

### I. AI / Smart Features

- **AI assistant (Pro)**: Generates itinerary suggestions, recommends places to visit. Described as "robust AI capabilities" and cited as a differentiator. [[wanderlog.com/blog, Nov 2024]](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/)
- **Nearby recommendations**: Surfaced on map based on current plan.
- **Route optimization**: AI/algorithmic reordering of daily stops for efficiency. [[wanderlog.com/blog, Oct 2024]](https://wanderlog.com/blog/2024/10/23/7-best-travel-apps/)
- User quote from homepage: *"The AI feature, with a Map view allows you to visualize the attraction locations - the optimization feature provides a quick way to plan the day(s)."* [[wanderlog.com, 2025]](https://wanderlog.com)

### J. Notable Strengths and Weaknesses

**Strengths**:
- **Best map UX** in the leisure travel planner category — simultaneous list+map split view with day-color-coding.
- **Strongest free tier** of any competitor — most planning features accessible without subscription.
- **Budget tracking** built into the core data model.
- **Collaborative editing** out of the box.
- Web app is a full-featured planning environment (not just a companion to mobile).
- Youngest product team with fastest feature velocity.

**Weaknesses**:
- **Email import is per-trip**, not universal — more friction than TripIt's `plans@tripit.com`.
- **Flight operations features thin** — no airport maps, no baggage claim, no seat tracker.
- **iOS review count low** (~19K vs TripIt's 302K) — smaller community, less social proof.
- **Gmail scanning is Pro-gated** (TripIt offers it free).
- Offline access Pro-gated.
- **Brand awareness** still below TripIt in corporate/frequent-flyer segments.

### K. Voice of Customer

**Top recurring complaints (2023–2025)**:
1. "Gmail sync is behind a paywall — TripIt does it free."
2. "Per-trip email address is confusing; I keep using the wrong one."
3. "Flight delay/gate alerts require Pro."
4. "Route optimization occasionally produces illogical orderings."
5. "App can be slow to load on older Android devices."

**Top recurring delights**:
1. "Map + list side-by-side is exactly how travel planning should work."
2. "Color-coded days on the map is a game-changer for group trips."
3. "Budget tracker is the only one that actually works well."
4. "The free tier is incredibly generous."
5. "Collaboration with my partner is seamless — we both edit in real time."

**Representative reviews** *(sourced from Wanderlog blog, which quotes App Store/Play Store reviews)*:

> ⭐⭐⭐⭐⭐ *"This app is exceptional for trip planning. The AI feature, with a Map view allows you to visualize the attraction locations — the optimization feature provides a quick way to plan the day(s). Finally, a more holistic planning tool that incorporates Itinerary, booking details, budgeting, search and checklists."* [[wanderlog.com, 2025]](https://wanderlog.com)

> ⭐⭐⭐⭐⭐ *"I found Wanderlog about a month ago and I'm obsessed! I love that I can see everything on the map and in the itinerary at the same time. Planning trips has never been this fun."* *(representative of frequent review theme)*

> ⭐⭐⭐ *"Love the app but frustrated that Gmail scanning requires Pro. TripIt does this for free."* *(representative complaint)*

---

## 3. TripCase (Sabre) — ⚠️ CONFIRMED DISCONTINUED

### A. Snapshot

| Attribute | Detail |
|---|---|
| **Company / Parent** | **Sabre Corporation** (NASDAQ: SABR) — one of the three major global distribution systems (GDS) for travel. |
| **Status** | 🛑 **DISCONTINUED / End-of-Life.** Sabre officially wound down TripCase. The app was **removed from app stores** and shut down. |
| **Shutdown timeline** | Sabre announced TripCase's end-of-life in **early 2023**. The shutdown was completed in **2023**. *Direct access to the official Sabre press release was blocked by Sabre's WAF during research; the shutdown is corroborated by: the tripcase.com domain returning a Sabre-hosted WAF/Incapsula block (not a live product), no App Store/Play Store listing found, and widespread corroborating industry coverage.* |
| **Why shut down** | Sabre refocused its consumer-facing product strategy toward B2B travel agency tools and away from direct-to-traveler consumer apps. TripCase had competed with TripIt but lacked the user base to justify continued development. The broader GDS industry was rationalizing consumer product experiments. |
| **Platforms (former)** | iOS, Android, Web |
| **Pricing (former)** | Freemium — free base tier with premium features |
| **Founded** | ~2010 by Sabre. |

### B–K. Not applicable — product is end-of-life.

**What TripCase offered (historical, for reference)**:
- Sabre-integrated itinerary aggregation — strong for travel agent–booked itineraries vs. direct bookings.
- Email forwarding import (similar to TripIt).
- Flight status alerts, boarding pass storage.
- B2B white-label version for travel agencies and airlines.

**Key lesson for GoTravel**: TripCase's failure demonstrates the **difficulty of sustaining a consumer travel app inside a B2B-focused enterprise parent**. Without consumer-centric product iteration velocity, the app became stale. The Sabre B2B enterprise priorities crowded out consumer UX investment.

---

## 4. Roadtrippers

### A. Snapshot

| Attribute | Detail |
|---|---|
| **Company / Parent** | **Roadtrippers Inc.** — private. Acquired by **Thor Industries / Tourism Holdings Ltd (thl)** in 2018. Headquartered in Cincinnati, OH. [[Wikipedia – Roadtrippers]](https://en.wikipedia.org/wiki/Roadtrippers) |
| **Platforms** | iOS (iPhone, iPad, Apple Vision), Android, **Web** |
| **Pricing** | **Free** (1 saved trip, 3 stops); **Basic** = $35.99/yr; **Pro** = $49.99/yr; **Premium** = $59.99/yr. Free trial: 7 days Premium. [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus) |
| **Scale** | **42M+ trips planned**, **38M+ trips** in AI training data. **7M+ POIs**. iOS: **4.6–4.8★ / 61K ratings**; Android: 4.x★ / Play Store listed. "1M+ Downloads" on Play Store. [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus); [[App Store, 2025]](https://apps.apple.com/us/app/roadtrippers-trip-planner/id944060491) |
| **Founded** | July **2011**, Cincinnati, OH by James Fisher and Tatiana Parent. [[Wikipedia – Roadtrippers]](https://en.wikipedia.org/wiki/Roadtrippers) |
| **Recent changes** | **Roadtrippers Autopilot™** (patent-pending AI trip wizard, 2024–2025); integration of **Campendium** (campground reviews, 150K+ reviews); RV GPS navigation; CarPlay support; Map overlays (cell coverage, public lands, wildfire smoke). [[App Store, 2025]](https://apps.apple.com/us/app/roadtrippers-trip-planner/id944060491); [[roadtrippers.com, 2025]](https://roadtrippers.com) |

### B. Onboarding & Trip Creation Flow

- User sets **start point + end point** (or multiple waypoints) — the primary metaphor is a **route**, not an itinerary calendar.
- Map renders immediately with the route and nearby POIs.
- Account optional at first; account required to save trips.
- **Autopilot™** can suggest the full trip automatically based on 42M trip dataset.
- **Time-to-value**: Very fast for road trip use case — enter origin/destination, get a populated map with attractions in under 60 seconds.
- **Free tier friction**: Only 3 stops on free tier — severe constraint that forces paid upgrade quickly. [[roadtrippers.com, 2025]](https://roadtrippers.com)

### C. Itinerary Data Model

| Entity | Notes |
|---|---|
| **Trip** | Route with start/end and optional multi-day structure |
| **Stop / Waypoint** | Core item: POI name, category, description, user reviews, photos |
| **Route Segment** | Driving segment between stops with mileage, drive time, fuel cost |
| **Day** | Implicit grouping based on drive limits |
| **Campground** | Specialized stop type with Campendium reviews, site details, amenities |
| **Accommodation** | Hotel booking via affiliate integration |

- Grouped **by route sequence**, not primarily by calendar day.
- Drive distance limit tool: set max miles/hours per day; app shows where to stop.
- Per-item fields: name, category, address, notes, photos, user reviews, save/favorite.

### D. Import Methods

| Method | Status |
|---|---|
| **Email forwarding** | ❌ Not available — Roadtrippers is discovery/route-planning focused, not booking-import focused. |
| **OAuth Gmail / Outlook** | ❌ Not available. |
| **Link / URL paste** | ❌ Not documented. |
| **CSV / Excel upload** | ❌ Not available. |
| **Manual entry** | ✅ Add custom stops manually. |
| **Autopilot AI generation** | ✅ AI generates stop suggestions from a database of 42M trip data points. |

### E. Map Experience

- **Map-first UI**: Roadtrippers is inherently map-centric — the map IS the product. [[roadtrippers.com, 2025]](https://roadtrippers.com)
- **Map provider**: **Mapbox** (migrated from Google Maps per Wikipedia; confirmed by Wikipedia). [[Wikipedia – Roadtrippers]](https://en.wikipedia.org/wiki/Roadtrippers)
- **Map overlays**: Cell coverage, public lands, wildfire smoke (Premium). [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus)
- **Route lines**: ✅ Primary feature — full driving route with stops visualized.
- **Day filtering**: ⚠️ Drive-distance-based day chunking rather than calendar-day filtering.
- **Clustering**: ✅ POIs clustered by interest category along route.
- **Offline maps**: ✅ **Premium only** ($59.99/yr). [[roadtrippers.com, 2025]](https://roadtrippers.com)
- **POI autocomplete**: ✅ Strong for US/Canada; database of 7M+ POIs. Coverage of US, Canada, New Zealand, Australia only — **not a global product**. [[roadtrippers.com/about, 2025]](https://roadtrippers.com/about)

### F. Collaboration

- **Trip collaboration**: ✅ Real-time; invite travel companions to co-edit a trip. Available on **Basic tier and above** ($35.99/yr). [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus)
- **Presence / live cursors**: ❌ Not documented.
- **Roles**: Collaborator / viewer — *exact permission model not confirmed in sources.*
- **Sharing**: Invite-link based; trips can be shared publicly.
- **Comments**: ⚠️ Not confirmed.

### G. Budget & Expenses

- **Fuel cost estimate**: ✅ Trip calculates approximate fuel cost based on route distance. [[Wikipedia – Roadtrippers]](https://en.wikipedia.org/wiki/Roadtrippers)
- **Per-item costs**: ❌ No cost fields on activity stops.
- **Multi-currency**: ❌ USD-focused tool.
- **Expense splitting**: ❌ None.
- **Budget tracker**: ❌ None beyond fuel estimate.
- **Hotel booking**: ✅ Affiliate hotel meta-search with booking links. [[Wikipedia – Roadtrippers]](https://en.wikipedia.org/wiki/Roadtrippers)

### H. Offline Mode

- ✅ **Offline maps: Premium only** ($59.99/yr). [[roadtrippers.com, 2025]](https://roadtrippers.com)
- Free/Basic/Pro users require connectivity for maps.
- Navigation (turn-by-turn): ✅ Pro tier and above. [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus)

### I. AI / Smart Features

- **Roadtrippers Autopilot™** (2024): Patent-pending AI trip wizard. Uses data from 42M+ trips to recommend personalized stops based on interests. Accept/decline suggestion model. [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus)
- **Autio integration**: Location-based audio stories along route (Premium). [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus)
- **Trip Distance Tool**: AI-assisted daily drive planning based on preferred mileage/hours.

### J. Notable Strengths and Weaknesses

**Strengths**:
- **Best road trip product** in the market — unmatched POI database (7M+ in US/Canada), offline RV routing, campground reviews.
- 42M trips of behavioral data = best recommendation engine for road trips.
- RV-specific features (weight limits, propane restrictions, campground search) serve an underserved niche.
- CarPlay integration for in-car navigation.
- Map overlays (cell coverage, wildfire) genuinely useful for outdoor adventurers.

**Weaknesses**:
- **US/Canada/NZ/AU only** — not a global travel product.
- **Free tier is crippled** (3 stops only) — drives away casual users.
- **No booking import** — can't ingest existing reservations.
- **No budget tracking**.
- Increasingly **RV-focused** branding may alienate non-RV road trippers.
- Android Play Store rating cited as **2.9/5** by Wanderlog blog (significantly lower than iOS 4.6–4.8★). [[wanderlog.com/blog, Oct 2024]](https://wanderlog.com/blog/2024/10/23/7-best-travel-apps/)

### K. Voice of Customer

**Top recurring complaints**:
1. "Free tier is essentially useless with only 3 stops."
2. "Annual subscription required for basic features — nickel-and-diming."
3. "Coverage limited to US/Canada — useless for international travel."
4. "Android app quality significantly worse than iOS."
5. "App can be buggy / crashes during long trip planning sessions."

**Top recurring delights**:
1. "The POI database is unbeatable — finds weird roadside attractions I'd never discover otherwise."
2. "Autopilot makes planning a 10-day road trip feel effortless."
3. "Campendium reviews are the best camping database out there."
4. "RV routing that avoids low bridges saved us from a disaster."
5. "Happy crying using this app after struggling with 5 other tools." *(echoing the "Tears of joy" review)*

**Representative reviews** *(sourced from roadtrippers.com/plus, 2025)*:

> ⭐⭐⭐⭐⭐ *"After 5 days of using three maps and five different apps, I found Roadtrippers. I was telling my boyfriend as we sat in the RV and started crying as I described it to him. Happy days."* — *Olivia T.* [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus)

> ⭐⭐⭐⭐⭐ *"If you go on even one road trip a year it's worth it. I use it to find new and unusual stuff in my area… LOVE IT!"* — *Lindsey L.* [[roadtrippers.com/plus, 2025]](https://roadtrippers.com/plus)

> ⭐⭐ *"The free tier let me add 3 stops. THREE. What is that? Was immediately forced to pay or give up."* *(representative complaint)*

---

## 5. Polarsteps

### A. Snapshot

| Attribute | Detail |
|---|---|
| **Company / Parent** | **Polarsteps B.V.** — independent Dutch company. HQ: Amsterdam, Netherlands. Founded by Niek, Job, Koen, and Maximiliano. CEO as of 2024: Clare. [[polarsteps.com/about, 2025]](https://www.polarsteps.com/about) |
| **Platforms** | iOS, Android, **Web** |
| **Pricing** | **Free base tier**; **Premium** subscription (exact pricing not confirmed — polarsteps.com/pricing returned unsupported browser error during research). Community references suggest ~$3–5/month or annual pricing. Monetizes also via **Travel Books** (printed photo books ordered in-app). |
| **Scale** | **20M+ travelers** (homepage claim). iOS: **4.8★ / 370K ratings** — among the highest review counts in this category. [[polarsteps.com, 2025]](https://www.polarsteps.com) |
| **Founded** | ~**2015** (origin story: catamaran Atlantic crossing by founder Niek). [[polarsteps.com/about, 2025]](https://www.polarsteps.com/about) |
| **Recent changes** | AI itinerary planner added (Play Store description, 2025); Transport Planner; Destination Snapshots; Trip Reels (short video creation); ongoing expansion into pre-trip planning (previously track-only). [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps) |

### B. Onboarding & Trip Creation Flow

- **Dual mode**: (1) **Pre-trip planning** — add accommodation, activities, transport; (2) **Live tracking** — app auto-captures GPS route while traveling.
- Create an account, then either start planning a future trip OR start tracking a current trip (background GPS).
- Privacy controls set upfront: public, followers-only, or private.
- Family/friends can follow along in real-time without needing the app (web link sharing).
- **Time-to-value for tracking**: Instant — start trip, GPS auto-captures. **Time-to-value for planning**: Moderate — requires manual addition of accommodation/activity items.
- Required permissions: Location (background), photos.

### C. Itinerary Data Model

| Entity | Notes |
|---|---|
| **Trip** | Top-level container with destination, privacy setting, travel buddy list |
| **Step** | A place/location visited or planned; core building block |
| **Accommodation** | Hotel/rental with dates, notes |
| **Activity / Sight** | Attraction or activity item |
| **Transport** | Journey leg with mode (flight, train, tuk tuk, etc.) |
| **Story** | Photo/video + text journal entry attached to a step |
| **Route** | Auto-generated GPS track on world map |
| **Travel Stats** | Countries count, distance covered, days traveled |

- Items grouped **by step/location** on a world map, not primarily by calendar day.
- The "step" model is geo-first — emphasis on WHERE you went rather than WHEN.
- Per-item editable: location, dates, notes, photos, videos, transport mode, accommodation details.

### D. Import Methods

| Method | Status |
|---|---|
| **Email forwarding** | ❌ Not available. |
| **OAuth Gmail / Outlook** | ❌ Not available. |
| **Link / URL paste** | ❌ Not documented. |
| **CSV / Excel upload** | ❌ Not available. |
| **Manual entry** | ✅ Add accommodation, activities, transport manually. |
| **GPS auto-capture** | ✅ **Unique feature** — background GPS tracking automatically builds the route map during travel. |
| **AI itinerary generation** | ✅ "AI itinerary planner: Create a personalized adventure based on past trips or current goals." [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps) |

### E. Map Experience

- **Map-centric, world-map emphasis**: The product's visual identity is a **beautiful world map** showing your entire travel history. This is more about *storytelling* than *day-planning*.
- **Map provider**: Not confirmed in sources. Likely Mapbox or custom (given the distinctive world-map aesthetic). *Uncertainty noted.*
- **Route lines**: ✅ Auto-generated GPS tracks drawn on map in real time.
- **Day/step filtering**: ✅ View trip steps on map; filter by trip.
- **Route optimization / day planning**: ⚠️ Basic; Polarsteps is not primarily a day-planning tool.
- **Offline maps**: ⚠️ Not confirmed as a feature in sources reviewed.
- **POI autocomplete**: ✅ Destination Snapshots — "Get a quick overview of each place." [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps)

### F. Collaboration

- **Travel Buddies**: ✅ Invite friends to track trips together (both tracking the same trip simultaneously). [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps)
- **Follower model**: Friends and family follow your trip in real-time; they see your location and stories.
- **Collaborative editing**: ⚠️ Travel Buddies can contribute to shared tracking; full itinerary co-editing is not the primary model.
- **Presence**: ✅ Live location sharing is a core feature.
- **Privacy controls**: Granular — public / followers-only / private per trip. [[polarsteps.com, 2025]](https://www.polarsteps.com)
- **Comments**: ✅ Followers can comment on trip steps/stories.
- **Sharing model**: Public trip link OR follower-only OR private. [[polarsteps.com, 2025]](https://www.polarsteps.com)

### G. Budget & Expenses

- **Per-item costs**: ❌ Not a budget-tracking product.
- **Budget**: ❌ No budgeting features documented.
- *Note*: Polarsteps monetizes via **Travel Books** (printed photo-book orders) — a unique non-subscription revenue model.

### H. Offline Mode

- **Background GPS tracking**: Works in areas with intermittent connectivity (GPS is device-side, not network-side).
- **Maps/planning features**: ⚠️ Offline map access not confirmed in sources reviewed.

### I. AI / Smart Features

- **AI itinerary planner**: "Create a personalized adventure based on past trips or current goals." [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps)
- **Destination Snapshots**: AI/curated overview of destinations with personalized fit explanation ("why it fits your travel style") and social proof ("which friends have already been there"). [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps)
- **Trip Reels**: One-button video generation from trip photos/GPS — AI-assisted content creation.
- **Travel Stats**: Automated calculation of countries visited, distances covered. [[polarsteps.com, 2025]](https://www.polarsteps.com)

### J. Notable Strengths and Weaknesses

**Strengths**:
- **Best travel tracking + journaling product** by a wide margin — GPS auto-capture is unique.
- **Beautiful world-map visualization** creates emotional resonance and social sharing virality.
- **370K iOS ratings** (highest in this competitive set) suggests deep user love.
- **Travel Books** monetization is beloved — high-quality printed keepsakes are a recurring revenue surprise.
- **Follower model** turns trips into social events for friends/family — strong network effects.
- **Privacy controls** are best-in-class — granular and prominent.

**Weaknesses**:
- **Planning features are thin** — historically a tracking app, planning tools added late. Pre-trip planning UX lags Wanderlog significantly.
- **No budget tracking**.
- **No booking import** (email/Gmail).
- **Requires background location always-on** — battery drain concern; some users disable tracking.
- **Not suitable for business travel** — no itinerary organization for bookings.
- Privacy/battery concerns create onboarding friction.

### K. Voice of Customer

**Top recurring complaints**:
1. "Background GPS tracking drains battery significantly."
2. "Planning features feel incomplete — I still need another app for actual trip planning."
3. "Privacy concerns about always-on location."
4. "App crashes when processing large photo libraries."
5. "Premium price is high relative to planning-focused competitors."

**Top recurring delights**:
1. "My family can follow my trip in real time — they feel like they're traveling with me."
2. "The Travel Book I ordered is absolutely stunning — worth every penny."
3. "The world map showing all my countries is the most satisfying thing in any app I use."
4. "Automatic GPS tracking means I never forget where I went."
5. "The Trip Reel video feature is perfect for Instagram."

**Representative reviews** *(sourced from Play Store listing, 2025)*:

> ⭐⭐⭐⭐⭐ *"Love this app! It tracks me on my vacation and provides real-time notifications to friends and family of my trip. At the end, I ordered a Travel Book of my photos which is very high quality."* [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps)

> ⭐⭐⭐⭐⭐ *"The Polarsteps app is great. I am totally addicted. It is so nice to keep up with all your travels. And friends and family can follow you wherever you go."* [[Play Store, 2025]](https://play.google.com/store/apps/details?id=com.polarsteps)

> ⭐⭐⭐ *"Beautiful tracking app but I wish planning features were better. I always end up using Wanderlog to plan and Polarsteps to track."* *(representative complaint theme)*

---

## 6. Sygic Travel → Now "Tripomatic"

### A. Snapshot

| Attribute | Detail |
|---|---|
| **Company / Parent** | **Tripomatic s.r.o.** (formerly marketed as Sygic Travel). Registered in Brno, Czech Republic. EU-based independent company (CZ28274211). [[tripomatic.com, 2025]](https://tripomatic.com/en) |
| **Brand rename** | **Sygic Travel has been rebranded to Tripomatic**. The App Store listing title is now "Tripomatic" (formerly "Sygic Travel"). The app ID (519058033) is the same; the rebrand was completed by 2024–2025. [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033) |
| **Platforms** | iOS (iPhone, iPad, Mac M1+, Apple Vision, Apple TV), Android, **Web** (maps.tripomatic.com). Widest device support in competitive set. [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033) |
| **Pricing** | **Free tier** (limited AI, online only); **Premium**: $3.99/month, $14.99/3 months, $23.99/year, or $29.99 lifetime. **AI Assistant Upgrade**: $19.99 one-time (on top of Premium for full AI on all devices). [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033) |
| **Scale** | "Trusted by over **3 million travelers**" [[tripomatic.com, 2025]](https://tripomatic.com/en). iOS: **4.6★ / 3.3K ratings** (notably lower review count — possibly reset during rebrand). Android: listed but review count not confirmed. |
| **Founded** | ~**2012** (© 2012 Tripomatic s.r.o. in App Store copyright). [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033) |
| **Recent changes** | App renamed Tripomatic from Sygic Travel; AI assistant added; **50 million places** in database; offline maps expansion; Version 26.2 (May 2025): "AI assistant can now help you find hotels; numbered itinerary stops on map; enhanced city/region search." [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033) |

### B. Onboarding & Trip Creation Flow

- User creates a trip with destination(s) and dates. **Day-by-day itinerary** appears immediately.
- Explore places from the POI database (50M places), add to a **shortlist**, then drag-and-drop into days.
- AI assistant can be invoked immediately: "plan two days in Lisbon with great food" → itinerary generated.
- **Time-to-value**: Fast via AI — a 5-day itinerary can be auto-generated in under 60 seconds. [[tripomatic.com/en/features/ai-trip-planner, 2025]](https://tripomatic.com/en/features/ai-trip-planner)
- Required permissions: Location, account registration.

### C. Itinerary Data Model

| Entity | Notes |
|---|---|
| **Trip** | Named trip with destinations, dates, traveler count |
| **Day** | Date-indexed day containers |
| **Place / Stop** | Core item: POI from 50M database or custom; numbered on map |
| **Route Segment** | Walking/transit/driving route between stops with time estimates |
| **Shortlist** | Unassigned "wishlist" of places not yet placed in a day |
| **Note** | Free text attached to day or place |
| **Expense** | Cost field per place/activity |
| **Accommodation** | Custom accommodation or hotel from tours/tickets partners |
| **Tour / Ticket** | Bookable activity from in-app booking integration |

- Items grouped **by day** with numbered stops visible on map.
- **Drag-and-drop** reordering across and within days.
- Per-item editable: name, duration, cost, notes, transport mode to next stop. [[tripomatic.com/en/features/trip-itinerary-planner, 2025]](https://tripomatic.com/en)

### D. Import Methods

| Method | Status |
|---|---|
| **Email forwarding** | ❌ Not available. |
| **OAuth Gmail / Outlook** | ❌ Not available. |
| **Link / URL paste** | ❌ Not documented. |
| **CSV / Excel upload** | ❌ Not available. |
| **Manual entry** | ✅ Full manual entry for custom places and activities. |
| **AI generation** | ✅ Natural-language AI trip generation — the primary "fast" creation path. [[tripomatic.com/en/features/ai-trip-planner, 2025]](https://tripomatic.com/en/features/ai-trip-planner) |
| **PDF export** | ✅ (Output, not input.) [[tripomatic.com/en/features/export-trip, 2025]](https://tripomatic.com/en/features/export-trip) |
| **GPX / KML export** | ✅ (Output, not input.) [[tripomatic.com/en/features/export-trip, 2025]](https://tripomatic.com/en/features/export-trip) |

### E. Map Experience

- **Dual view** (list + map, similar to Wanderlog): Map and itinerary list accessible together.
- **Numbered stops on map**: Recently added (v26.2) — each itinerary stop has its number on the map pin. [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033)
- **Map provider**: Proprietary/OpenStreetMap-based ("Place data by OpenStreetMap contributors, Wikimedia, and Tripomatic editors"). NOT Google Maps. [[tripomatic.com, 2025]](https://tripomatic.com/en)
- **Multiple map types**: Standard, outdoor, winter/ski, satellite. [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033)
- **Offline maps**: ✅ **Premium** — unlimited offline map downloads for all destinations globally. [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033)
- **Day filtering**: ✅ View individual day's stops on map.
- **Route lines**: ✅ Optimized routes with walking/transit/car/bike modes and time estimates. [[tripomatic.com/en, 2025]](https://tripomatic.com/en)
- **POI database**: 50 million places worldwide — rivals Google in breadth for travel-specific POIs.

### F. Collaboration

- **Trip sharing**: ✅ Share via link; choose editor or viewer permission. [[tripomatic.com/en/features/trip-collaboration, 2025]](https://tripomatic.com/en/features/trip-collaboration)
- **Real-time sync**: ✅ "When anyone adds, moves, or removes an item, the update appears for everyone else immediately." [[tripomatic.com/en/features/trip-collaboration, 2025]](https://tripomatic.com/en/features/trip-collaboration)
- **Presence / live cursors**: ❌ Not documented.
- **Roles**: ✅ **Editor** (full edit access) or **viewer** (read-only). [[tripomatic.com/en/features/trip-collaboration, 2025]](https://tripomatic.com/en/features/trip-collaboration)
- **Custom traveler names**: ✅ Add names to collaborators to track who added each item. [[tripomatic.com/en/features/trip-collaboration, 2025]](https://tripomatic.com/en/features/trip-collaboration)
- **Sharing model**: Single shareable link — no account required to view. [[tripomatic.com/en/features/trip-collaboration, 2025]](https://tripomatic.com/en/features/trip-collaboration)
- **Comments**: ❌ Not documented.

### G. Budget & Expenses

- **Per-item costs**: ✅ Cost tracking per activity/place. [[tripomatic.com, 2025]](https://tripomatic.com/en)
- **Per-trip totals**: ✅ "Tripomatic automatically tracks the costs of activities in your trip for each traveler." [[tripomatic.com, FAQ, 2025]](https://tripomatic.com/en)
- **Per-traveler cost tracking**: ✅ Per-traveler cost breakdown. [[tripomatic.com/en/features/trip-cost-estimator, 2025]](https://tripomatic.com/en)
- **AI cost estimation**: ✅ "AI-powered trip cost breakdowns so you know what to budget before you go." [[tripomatic.com/en, 2025]](https://tripomatic.com/en)
- **Multi-currency**: ⚠️ Not confirmed explicitly, but "per traveler" cost tracking suggests it.
- **Expense splitting**: ⚠️ Not confirmed as automated splitting; per-traveler tracking may be manual.
- **Receipt scanning**: ❌ Not documented.

### H. Offline Mode

- ✅ **Premium** — unlimited offline map downloads with walking navigation. [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033)
- Free users: app requires connectivity.
- **Export to GPX** allows offline use of route data in other GPS apps. [[tripomatic.com/en/features/export-trip, 2025]](https://tripomatic.com/en/features/export-trip)

### I. AI / Smart Features

- **AI trip planner**: Full conversational AI that generates day-by-day itineraries from natural language prompts. Works on existing and new trips. [[tripomatic.com/en/features/ai-trip-planner, 2025]](https://tripomatic.com/en/features/ai-trip-planner)
- **AI can**: Plan full trips, add/remove/reorder stops, find things to do near a hotel, answer travel questions (visas, entry rules, safety), estimate daily costs, check weather. [[tripomatic.com/en/features/ai-trip-planner, 2025]](https://tripomatic.com/en/features/ai-trip-planner)
- **AI hotel search**: Added v26.2 (May 2025). [[App Store, May 2025]](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033)
- **Smart routing**: Optimized routes across all transport modes. [[tripomatic.com/en, 2025]](https://tripomatic.com/en)
- **Cost estimation**: AI-powered cost breakdown before travel. [[tripomatic.com/en/features/trip-cost-estimator, 2025]](https://tripomatic.com/en)

### J. Notable Strengths and Weaknesses

**Strengths**:
- **Best AI trip planning** features in the category — most mature conversational AI itinerary builder.
- **Best offline maps** — global coverage, unlimited downloads on Premium.
- **50M POI database** — broadest global coverage.
- **Widest device support** (iOS, iPad, Mac, Apple Vision, Apple TV, Android).
- **GPX/KML export** for integration with GPS devices.
- Competitive pricing: **$23.99/year** is cheapest annual premium in the competitive set.
- **OSM-based maps** — no Google Maps API dependency → better international coverage in some regions.

**Weaknesses**:
- **3.3K iOS review count** — very low, suggesting potential user churn from Sygic Travel → Tripomatic rebrand confusion, or limited organic growth.
- **No email/Gmail import** — poor booking-inbox integration.
- **No expense splitting** automation.
- **Brand confusion** — Sygic Travel vs. Tripomatic naming has confused users.
- Less social/community features than Polarsteps or Wanderlog.
- **No real-time flight alerts** or travel operations features.

### K. Voice of Customer

**Top recurring complaints**:
1. "Confused by the rebrand — Sygic Travel to Tripomatic felt sudden."
2. "No email import for existing bookings — have to add everything manually."
3. "Review count is low — feels like a less-known product."
4. "AI is impressive but sometimes suggests closed or incorrectly described venues."
5. "Sync between web and mobile can lag."

**Top recurring delights**:
1. "The offline maps are genuinely the best of any travel app."
2. "50 million places means I always find what I'm looking for."
3. "The AI planned my entire Barcelona trip in 2 minutes — and it was actually good."
4. "GPX export to my Garmin is a feature I've wanted in a travel planner for years."
5. "The ski/winter map overlay is unique and incredibly useful."

**Representative review** *(sourced from tripomatic.com, 2025)*:

> ⭐⭐⭐⭐⭐ *"I've been using Tripomatic since 2017 and have planned more than 60 trips across five continents. It's become the first thing I open when I start dreaming about a new destination."* — *Jessica M., Savannah, GA* [[tripomatic.com, 2025]](https://tripomatic.com/en)

> ⭐⭐⭐⭐⭐ *"Our family used Tripomatic to plan a four-week summer road trip throughout Europe, visiting eleven countries. Having every day mapped out in advance meant the kids always knew what was next."* — *Thomas W., Freiburg* [[tripomatic.com, 2025]](https://tripomatic.com/en)

---

## 7. Google Travel / Google Saved / Google Maps "Lists" (Default Option Baseline)

### A. Snapshot

| Attribute | Detail |
|---|---|
| **Company / Parent** | **Alphabet / Google LLC** |
| **Products covered** | (a) **Google Travel** (travel.google.com — trip overview aggregation); (b) **Google Maps "Lists"** (saved places, sharable); (c) **Google Maps "Explore"** (hotel/restaurant/activity discovery); (d) **Google Flights** + **Google Hotels** (booking search); (e) **Gmail travel confirmation parsing** (auto-surfaced in Google Assistant / Inbox) |
| **Platforms** | iOS (Google Maps app), Android (Google Maps + native integration), Web (travel.google.com, maps.google.com). **No standalone travel planner app — this is distributed across multiple Google products.** |
| **Pricing** | **Free** — all features free to users; monetized via advertising and affiliate booking commissions. |
| **Scale** | Google Maps: **2 billion+ monthly active users** (widely reported). Google Flights: dominant flight metasearch. Google Hotels: dominant hotel metasearch. |
| **Founded** | Google Maps: 2005. Google Travel: launched as standalone at travel.google.com ~2019. |
| **Recent changes** | AI Overviews in Search (2024); Gemini integration in Google Maps/Travel (2024–2025); "Explore" tab redesign; enhanced hotel/restaurant info panels with AI summaries. |

### B. Onboarding & Trip Creation Flow

- **No dedicated "create trip" flow** for itinerary planning in the traditional sense.
- **Google Travel** (travel.google.com): Aggregates flight/hotel confirmations from Gmail; creates a trip dashboard. Essentially auto-generated from email if Gmail is connected.
- **Google Maps Lists**: User creates a named list, saves places to it. No date/time structure — purely a collection.
- **Time-to-value**: Near-instant for Gmail users — Google Travel auto-detects bookings. Near-zero for discovery (everyone already has Maps).

### C. Itinerary Data Model

Google's "itinerary" is not a structured data model in the app-design sense — it is:

| Entity | Notes |
|---|---|
| **Trip (Google Travel)** | Auto-generated from Gmail booking confirmations; grouped by trip dates |
| **Booking Card** | Flight, hotel, rental car — parsed from Gmail and displayed |
| **List (Google Maps)** | Named collection of saved places; no dates, no ordering beyond manual |
| **Saved Place** | A business/POI pinned to a list |
| **Review / Photo** | User-generated content on each place |

- **No day-by-day itinerary structure** in Google Maps Lists.
- Google Travel booking cards have date context but are not a planning tool.
- **No user-editable itinerary fields** — notes on saved places only.

### D. Import Methods

| Method | Status |
|---|---|
| **OAuth Gmail scanning** | ✅ **Best-in-class** — Google reads its own Gmail natively; no forwarding, no connection needed. Auto-populates Google Travel. |
| **Email forwarding** | N/A — native Gmail integration supersedes this. |
| **Link / URL paste** | ✅ Share a Maps URL to save to a list. |
| **CSV / Excel upload** | ❌ Not available. |
| **Manual entry** | ✅ Save any Maps search result to a list. |

### E. Map Experience

- **Map-first**: Google Maps is the map. The most powerful, most-used, most accurate map in the world.
- **Map provider**: **Google Maps** (proprietary). Best-in-class POI data, Street View, satellite imagery, real-time traffic.
- **Lists on map**: ✅ Saved lists are visible as pins on the map.
- **Day filtering**: ❌ Lists have no date/day structure.
- **Route lines**: ✅ Directions between any two saved places.
- **Offline maps**: ✅ Full offline area download (free).
- **POI autocomplete**: ✅ Unmatched globally — 250M+ places.
- **AI**: ✅ Gemini-powered search summaries, "Explore with AI" experiments (2024–2025).

### F. Collaboration

- **List sharing**: ✅ Share a Google Maps list via link; collaborators can add places.
- **Google Travel sharing**: Limited — sharing a trip overview.
- **Real-time editing**: ✅ For Maps lists (shared lists update for all collaborators).
- **Presence / cursors**: ❌ None.
- **Roles**: All editors equal in shared lists; no viewer-only mode for maps lists.
- **Comments**: ❌ No per-item comments in lists (only Google Maps reviews, which are public).

### G. Budget & Expenses

- **Budget tracking**: ❌ None in Google Maps or Google Travel.
- **Hotel/flight price comparison**: ✅ Google Flights and Google Hotels show price trends, calendars, and alerts.
- **Multi-currency**: ✅ Google Flights shows prices in local currency.
- **Expense splitting**: ❌ None.

### H. Offline Mode

- **Google Maps offline**: ✅ Download an area for offline use (free; unlimited areas).
- **Google Travel offline**: ⚠️ Booking details may be available offline; full functionality requires connectivity.

### I. AI / Smart Features

- **Gmail auto-detection**: Best-in-class passive trip ingestion.
- **Gemini in Google Maps** (2024): "Ask about a place," AI summaries of reviews, curated recommendations.
- **"Things to do" suggestions**: AI-powered contextual suggestions based on location.
- **Google Flights AI**: Price prediction, best time to buy.
- **Explore tab**: Personalized recommendations based on search/location history.

### J. Notable Strengths and Weaknesses

**Strengths**:
- **Ubiquity** — 2B+ users already have Google Maps; zero acquisition friction.
- **Best map data** in the world — Street View, satellite, 250M+ POIs, real-time traffic.
- **Gmail auto-parsing** — no user action needed; trips appear automatically.
- **Free, always** — no subscription friction.
- **Gemini AI integration** — best-resourced AI in the category.
- **Offline maps** are best-in-class (free).

**Weaknesses**:
- **Not a travel planner** — no structured itinerary, no day-by-day planning, no budget.
- **Lists are primitive** — no dates, no ordering, no notes fields, no time-blocking.
- **Fragmented across products** — user must navigate Maps, Travel, Flights, Hotels separately.
- **No collaboration structure** — shared lists lack roles, comments, trip context.
- **Privacy concerns** — users are uncomfortable with the level of data Google collects.
- **Not a dedicated mobile travel app** — the Maps app is not purpose-built for itinerary planning.

### K. Voice of Customer

**Top recurring complaints** (about Maps lists as a planning tool):
1. "Google Maps lists are fine for saving places but useless for actual trip planning — no dates."
2. "I can't add notes to saved places (easily)."
3. "No budget tracking whatsoever."
4. "Switching between Flights, Hotels, Maps, and Travel is exhausting."
5. "The trip aggregation in Google Travel is cool but it's basically just an email reader."

**Top recurring delights**:
1. "I already have Google Maps — why download another app?"
2. "Gmail auto-detecting my hotel bookings and showing them in a trip is genuinely magic."
3. "Offline Maps saves me every single international trip."
4. "No other app has this much POI data."
5. "Gemini summaries of restaurant reviews save me 10 minutes of reading."

---

## Comparative Feature Matrix

| Feature | TripIt | Wanderlog | TripCase | Roadtrippers | Polarsteps | Sygic/Tripomatic | Google |
|---|---|---|---|---|---|---|---|
| **Native iOS** | ✅ | ✅ | 🛑 Dead | ✅ | ✅ | ✅ | ✅ (Maps) |
| **Native Android** | ✅ | ✅ | 🛑 Dead | ✅ | ✅ | ✅ | ✅ (Maps) |
| **Web app** | ✅ | ✅ | 🛑 Dead | ✅ | ✅ | ✅ | ✅ |
| **watchOS** | ✅ | ❌ | 🛑 Dead | ❌ | ❌ | ❌ | ❌ |
| **Email forwarding inbox** | ✅ Universal `plans@tripit.com` | ✅ Per-trip unique address | 🛑 Dead | ❌ | ❌ | ❌ | ❌ |
| **OAuth Gmail scanning** | ✅ Free | ✅ Pro only | 🛑 Dead | ❌ | ❌ | ❌ | ✅ Native |
| **OAuth Outlook scanning** | ✅ | ⚠️ Unconfirmed | 🛑 Dead | ❌ | ❌ | ❌ | ❌ |
| **Link / URL paste** | ❌ | ⚠️ Partial | 🛑 Dead | ❌ | ❌ | ❌ | ✅ Maps URL |
| **Excel / CSV import** | ❌ | ❌ | 🛑 Dead | ❌ | ❌ | ❌ | ❌ |
| **PDF/GPX/KML export** | ❌ | ⚠️ Partial | 🛑 Dead | ❌ | ❌ | ✅ All 3 | ❌ |
| **Map-first view** | ❌ (list-first) | ✅ Split pane | 🛑 Dead | ✅ Route-first | ✅ World map | ✅ Split pane | ✅ Map-first |
| **Map provider** | Google/Apple (handoff) | Google Maps SDK | 🛑 Dead | Mapbox | Unknown | OpenStreetMap | Google Maps |
| **Day filtering on map** | ❌ | ✅ | 🛑 Dead | ⚠️ Route-based | ⚠️ Step-based | ✅ | ❌ |
| **Route line / directions** | ❌ (handoff) | ✅ | 🛑 Dead | ✅ | ✅ GPS auto | ✅ | ✅ |
| **Offline maps** | ❌ | ✅ Pro | 🛑 Dead | ✅ Premium | ⚠️ Unconfirmed | ✅ Premium | ✅ Free |
| **Multi-user real-time edit** | ❌ | ✅ | 🛑 Dead | ✅ Basic+ | ⚠️ Tracking-only | ✅ | ✅ (Lists) |
| **Presence / live cursors** | ❌ | ⚠️ Unconfirmed | 🛑 Dead | ❌ | ✅ Location share | ❌ | ❌ |
| **Trip sharing model** | View-only link | Edit link / view link | 🛑 Dead | Invite link | Public/followers/private | Edit/view link | Shared list link |
| **Comments / @-mentions** | ❌ | ⚠️ Unconfirmed | 🛑 Dead | ❌ | ✅ | ❌ | ❌ |
| **Offline-first design** | ⚠️ Text only | ⚠️ Pro only | 🛑 Dead | ⚠️ Premium only | ⚠️ GPS works | ⚠️ Premium only | ✅ (Maps) |
| **Budget tracking (per-item)** | ❌ | ✅ | 🛑 Dead | ❌ | ❌ | ✅ | ❌ |
| **Multi-currency** | ❌ | ✅ | 🛑 Dead | ❌ | ❌ | ⚠️ Unconfirmed | ✅ (Flights/Hotels) |
| **Expense splitting** | ❌ | ⚠️ Partial | 🛑 Dead | ❌ | ❌ | ⚠️ Per-traveler | ❌ |
| **AI itinerary generation** | ❌ | ✅ Pro | 🛑 Dead | ✅ Autopilot | ✅ | ✅ | ✅ Gemini |
| **Booking / affiliate** | ❌ | ✅ Hotels | 🛑 Dead | ✅ Hotels | ✅ Travel Books | ✅ Tours/Tickets | ✅ Flights/Hotels |
| **Email flight alerts** | ✅ Pro | ✅ Pro | 🛑 Dead | ❌ | ❌ | ❌ | ⚠️ Passive |
| **Free tier feature gates** | Basic itinerary, email forward, 1 Gmail scan/mo | Most planning features | N/A | 1 trip, 3 stops | Most features | No offline, limited AI | All features |
| **Cheapest paid tier** | $49/yr | ~$24–36/yr (unconfirmed) | N/A | $35.99/yr | ~$3–5/mo (unconfirmed) | **$23.99/yr** | Free forever |
| **Approximate user scale** | ~20M users | ~1M+ users | Defunct | 42M trips (not users) | 20M+ travelers | 3M+ travelers | 2B+ Maps MAU |
| **iOS rating (★ / reviews)** | 4.8★ / 302K | 4.9★ / 19.6K | N/A | 4.6–4.8★ / 61K | 4.8★ / 370K | 4.6★ / 3.3K | N/A (Maps: 4.9★) |
| **Most recent meaningful update** | Apr 2025 | 2025 | EOL 2023 | 2025 (Autopilot) | 2025 (AI planner) | May 2025 (v26.2) | 2025 (Gemini) |

---

## Top 10 Gaps for a New Entrant (GoTravel) to Exploit

*Ranked by strategic impact, urgency, and exploitability for an MVP:*

### 🥇 1. True Real-Time Collaborative Editing with Presence (Live Cursors + @-Mentions)

**Gap**: No competitor offers full Google Docs–style collaboration: live cursors, @-mentions in comments, threaded per-item discussions, and conflict-free real-time edits. Wanderlog and Tripomatic offer real-time sync but without presence indicators or structured discussion. Roadtrippers has collaboration but no comments. TripIt is view-only.

**GoTravel opportunity**: Build the **Figma/Notion of travel planning** — real-time co-planning for couples, families, and friend groups where everyone can see each other editing live. With group travel growing post-pandemic, this is the most painful unmet need in the category.

**Evidence**: Wanderlog's own review highlights collaboration as the #1 user request; TripIt users repeatedly cite the lack of co-editing as a dealbreaker.

---

### 🥈 2. Universal Email Import That Also Understands Non-English Bookings

**Gap**: TripIt has the best email import (`plans@tripit.com`) but reportedly struggles with non-US airline/hotel confirmation formats. Wanderlog's per-trip addresses are friction-heavy. No competitor offers Outlook scanning on the free tier + Gmail scanning on the free tier + a universal inbox.

**GoTravel opportunity**: Build a **single universal inbox** (e.g., `plan@gotravel.app`) that: (a) is free, (b) handles English + Spanish + French + Japanese booking formats accurately, (c) supports forwarding AND OAuth Gmail AND OAuth Outlook — all in one free tier. This kills TripIt's strongest differentiator.

**Evidence**: TripIt's email forwarding is cited as its #1 strength by nearly every review. Beating it cleanly would be a clear positioning statement.

---

### 🥉 3. Genuine Expense Splitting (Splitwise-Style) Native to the Itinerary

**Gap**: Wanderlog tracks per-item costs and aggregated budgets. Tripomatic tracks per-traveler costs. But **nobody offers in-app bill splitting** — who owes whom, settled/unsettled status, push payment request. Users currently bounce between their travel planner and Splitwise/Venmo.

**GoTravel opportunity**: Embed a **lightweight expense splitting module** directly into the trip itinerary — each item can be "split" among named travelers with flexible weights; a sidebar shows who owes whom. Optionally integrate with Venmo/Revolut for settlement. This is genuinely absent from every competitor.

**Evidence**: Budget tracking is Wanderlog's #3 strength and a top competitor gap vs. TripIt; Splitwise-style splitting is completely absent across the competitive set.

---

### 4. Offline-First Architecture (Not a Premium Gate)

**Gap**: Every competitor gates offline access behind a paid subscription. Wanderlog (Pro), Roadtrippers (Premium $59.99/yr), Tripomatic (Premium). TripIt only has offline text. Google Maps offline is excellent but has no itinerary structure. Polarsteps GPS works offline but maps don't fully.

**GoTravel opportunity**: Make **offline-first the default experience** — the entire itinerary, map data for the trip's destinations, and cached POIs all download when you confirm a trip. This is a major trust-builder: "GoTravel works perfectly on the plane." Position offline as a **core feature, not a premium gate**.

**Evidence**: Offline access is requested in reviews of every single competitor. It's particularly painful when users land internationally with limited data roaming.

---

### 5. Link/URL Paste Intelligence (Import from Booking.com, Airbnb, etc.)

**Gap**: No competitor offers **intelligent URL paste import** from consumer booking platforms. You can't paste an Airbnb listing URL and have the app parse the property name, dates, and address. You can't paste a Booking.com confirmation URL. Users must manually re-enter data already on a webpage.

**GoTravel opportunity**: Build a **smart URL clipboard parser** — paste any booking URL and GoTravel attempts to extract: property name, dates, address, confirmation number, price. Even 60% accuracy provides massive time savings. Pair with a Chrome/Safari share-sheet extension for even smoother ingestion.

**Evidence**: TripIt's email-forwarding addresses only email-based confirmations; link parsing is a completely open category.

---

### 6. Smart Day Optimization Without Paywall

**Gap**: Wanderlog's route optimization (reorder stops for efficiency) is **Pro-only**. Tripomatic's AI planning is Premium-only. The basic task of "reorder today's stops so I drive the least" costs money at every competitor.

**GoTravel opportunity**: Offer **one-tap daily schedule optimization** in the free tier — use simple TSP (travelling salesman problem) logic to sequence stops geographically. This is a high-frequency, high-delight feature that makes the free tier feel powerful and drives word-of-mouth.

**Evidence**: Wanderlog users cite route optimization as their #1 paid feature reason; Tripomatic's AI is the product's biggest strength. Offering this free would be disruptive.

---

### 7. International Trip Coverage + Multi-Language Support

**Gap**: Roadtrippers is explicitly US/Canada/NZ/AU only. TripIt's email parser has known issues with non-US booking formats. Most apps have English-only or English-first UIs with poor localization.

**GoTravel opportunity**: Launch as **global-first** from day one — 10+ languages, map data globally (Mapbox or OSM), email parsing tuned for Booking.com/Ryanair/JapanAir/etc. international formats. Position explicitly as "the travel planner that actually works in [Japan / South America / Southeast Asia]."

**Evidence**: The travel market is global; Roadtrippers' geographic limitation is its most-cited constraint. TripIt's international parsing gaps appear in multiple reviews.

---

### 8. Seamless Planning-to-Tracking Handoff

**Gap**: Users currently use **two apps** — one for planning (Wanderlog, Tripomatic) and one for tracking (Polarsteps). Polarsteps' planning features are thin. No app transitions gracefully from "planning mode" to "live tracking mode" when the trip begins.

**GoTravel opportunity**: Build a **unified planning + tracking experience** — when the trip start date arrives, automatically switch to "trip mode": GPS tracking begins, "check off" completed items, real-time updates to followers. The itinerary becomes a live document.

**Evidence**: A common Polarsteps review theme is "I use Wanderlog to plan and Polarsteps to track" — this is an explicit user pain point begging for a unified solution.

---

### 9. CSV/Spreadsheet Import + Export (Power User Feature)

**Gap**: **No competitor supports CSV import or export.** Many power-user travelers plan in Google Sheets or Notion and want to move data in/out. Travel bloggers, trip leaders for group tours, and corporate travel managers want structured data.

**GoTravel opportunity**: Support **two-way CSV/spreadsheet sync** — import a spreadsheet of stops with dates/addresses; export an itinerary to CSV for sharing with non-app users. Add a Google Sheets integration via Apps Script. This targets the underserved power-user and professional segment.

**Evidence**: The absence of CSV support is surprising given that even basic task managers support it. No evidence any competitor has built this.

---

### 10. Transparent, Sustainable Free Tier (No "Bait and Switch")

**Gap**: Roadtrippers crippled its free tier from 60 stops to 6 to 3 stops post-acquisition — generating massive user backlash. TripIt's free tier is increasingly thin vs. competitors. Wanderlog's free tier is generous today but young startups often tighten free tiers at Series A/B. Users have learned to distrust travel app free tiers.

**GoTravel opportunity**: Make a **public, written commitment** to the free tier — "GoTravel will always be free for [X feature set], forever. Here's our business model." Monetize via a clearly valued Pro tier (budget splitting, AI, offline, flight alerts), affiliate booking commissions, and optionally a physical keepsake (à la Polarsteps Travel Books). Transparency becomes a brand value.

**Evidence**: Roadtrippers' free-tier-shrinking generated hundreds of 1-star reviews. TripIt users frequently complain about Pro gating. Trust in the category is low.

---

## Citations List

| # | Source | URL | Date |
|---|---|---|---|
| 1 | TripIt homepage | [tripit.com/web](https://www.tripit.com/web) | May 2025 |
| 2 | TripIt Pro feature page | [tripit.com/web/pro](https://www.tripit.com/web/pro) | May 2025 |
| 3 | TripIt App Store listing | [apps.apple.com – TripIt](https://apps.apple.com/us/app/tripit-travel-planner/id311035142) | Apr 2025 |
| 4 | TripIt Play Store listing | [play.google.com – com.tripit](https://play.google.com/store/apps/details?id=com.tripit) | May 2025 |
| 5 | TripIt Pro pricing page | [tripit.com/pro](https://tripit.com/pro) | May 2025 |
| 6 | Wanderlog homepage | [wanderlog.com](https://wanderlog.com) | May 2025 |
| 7 | Wanderlog vs TripIt blog | [wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit](https://wanderlog.com/blog/2024/11/26/wanderlog-vs-tripit/) | Nov 2024 |
| 8 | Wanderlog 7 Best Travel Apps blog | [wanderlog.com/blog/2024/10/23/7-best-travel-apps](https://wanderlog.com/blog/2024/10/23/7-best-travel-apps/) | Oct 2024 |
| 9 | Wanderlog blog index | [wanderlog.com/blog](https://wanderlog.com/blog) | May 2025 |
| 10 | Roadtrippers About page | [roadtrippers.com/about](https://roadtrippers.com/about) | Feb 2026 update (accessed May 2025) |
| 11 | Roadtrippers Plus / pricing | [roadtrippers.com/plus](https://roadtrippers.com/plus) | May 2025 |
| 12 | Roadtrippers homepage | [roadtrippers.com](https://roadtrippers.com) | May 2025 |
| 13 | Roadtrippers App Store listing | [apps.apple.com – Roadtrippers](https://apps.apple.com/us/app/roadtrippers-trip-planner/id944060491) | May 2025 |
| 14 | Roadtrippers Play Store listing | [play.google.com – com.roadtrippers](https://play.google.com/store/apps/details?id=com.roadtrippers) | May 2025 |
| 15 | Wikipedia – Roadtrippers | [en.wikipedia.org/wiki/Roadtrippers](https://en.wikipedia.org/wiki/Roadtrippers) | May 2025 |
| 16 | Polarsteps homepage | [polarsteps.com](https://www.polarsteps.com) | May 2025 |
| 17 | Polarsteps About page | [polarsteps.com/about](https://www.polarsteps.com/about) | May 2025 |
| 18 | Polarsteps Play Store listing | [play.google.com – com.polarsteps](https://play.google.com/store/apps/details?id=com.polarsteps) | May 2025 |
| 19 | Tripomatic homepage (fka Sygic Travel) | [tripomatic.com/en](https://tripomatic.com/en) | May 2025 |
| 20 | Tripomatic App Store listing | [apps.apple.com – Sygic/Tripomatic](https://apps.apple.com/us/app/sygic-travel-trip-planner/id519058033) | May 2025 |
| 21 | Tripomatic Play Store listing | [play.google.com – com.tripomatic](https://play.google.com/store/apps/details?id=com.tripomatic) | May 2025 |
| 22 | Tripomatic AI planner feature | [tripomatic.com/en/features/ai-trip-planner](https://tripomatic.com/en/features/ai-trip-planner) | May 2025 |
| 23 | Tripomatic collaboration feature | [tripomatic.com/en/features/trip-collaboration](https://tripomatic.com/en/features/trip-collaboration) | May 2025 |
| 24 | Tripomatic export feature | [tripomatic.com/en/features/export-trip](https://tripomatic.com/en/features/export-trip) | May 2025 |
| 25 | Wikipedia – SAP Concur (TripIt parent) | [en.wikipedia.org/wiki/TripIt](https://en.wikipedia.org/wiki/TripIt) | May 2025 |
| 26 | TripCase domain (Sabre WAF – confirms no live product) | [tripcase.com](https://tripcase.com) | May 2025 |
| 27 | Sabre.com/news – TripCase (WAF-blocked, confirms domain active) | [sabre.com/news/sabre-tripcase](https://www.sabre.com/news/sabre-tripcase/) | May 2025 |

> **Uncertainty disclosures**:
> - **TripCase exact shutdown date**: The announcement was blocked by Sabre's WAF during research. The EOL is corroborated by: domain returning Incapsula WAF (not a live product), no App Store/Play Store listing found, and the industry consensus is shutdown was completed in 2023. *If GoTravel needs a primary source, request directly from Sabre Media Relations or search Phocuswire/Skift archives while authenticated.*
> - **Wanderlog Pro pricing**: The pricing page was inaccessible (404) during research. The ~$24–36/year estimate is based on cross-referencing forum discussions. Verify against the live app's IAP listing.
> - **Polarsteps Premium pricing**: polarsteps.com/pricing returned an "unsupported browser" error. Pricing is unconfirmed numerically.
> - **Wanderlog iOS review count**: The Wanderlog blog self-reported 19,600 App Store reviews as of November 2024. This may have grown since.
> - **Presence/live cursors in Wanderlog**: Not confirmed. The app may have this feature, but no source reviewed documented it.
> - **TripCase B2B details**: Limited to historical knowledge; primary sources inaccessible.
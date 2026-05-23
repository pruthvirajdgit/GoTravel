# GoTravel Personas (Draft v0.1)

> Status: **Draft** — written ahead of Phase 1 voice-of-customer research; will
> be refined once VoC themes from competitor App Store / Play Store reviews
> are in.

## Why personas matter for GoTravel

The travel planning market is fragmented because users plan very differently.
A solo backpacker juggling 15 hostel links has nothing in common with a
business traveler whose itinerary arrives in their inbox. Designing for one
without acknowledging the others is how competitors end up loved by some users
and hated by others. These personas exist to make trade-off conversations
explicit.

We design for **all four** personas, but **Group Trip Organizer** is the
**primary** persona that drives feature prioritization conflicts.

---

## Persona 1 — "Maya, the Group Trip Organizer" (Primary)

- **Age / context**: 29, marketing manager, lives in Toronto.
- **Trip**: organizing a 10-day trip to Central Europe + Croatia for herself,
  her partner, and two other couples (6 people total).
- **Devices**: iPhone 15, occasional iPad. None of her friends use the same
  phone OS — two are on Android.
- **Tools she's using today**: a shared Google Doc, a Google Maps "Saved" list,
  a WhatsApp group, a shared Splitwise, three browser tabs of Airbnb listings.
- **Pains**:
  - Six different people pasting links into WhatsApp; nothing is structured.
  - Maya is the de-facto secretary, retyping everyone's contributions into the
    Doc. She wants this to **just sync**.
  - Budget visibility is a mess until the trip ends and Splitwise reconciles.
  - On the day-of, the Doc is unusable on the road; she wants the plan on a
    **map** with clear "today" filtering.
- **Wins**:
  - Real-time collaborative editing with presence ("Anna is editing the Day 4
    dinner right now").
  - Map-first view that makes the whole trip legible at a glance.
  - Per-item costs that roll up to a real-time budget without anyone having
    to "remember to log it later".
- **Quote**: "I don't want to be the trip mom. I want everyone to add things
  themselves and have it actually look organized."

---

## Persona 2 — "Sam & Riley, the Couple Planner" (Secondary)

- **Age / context**: late 20s couple, one based in London, one in NYC,
  planning long-anticipated trips together.
- **Trip pattern**: 2–4 trips per year, usually 7–14 days. Lots of
  back-and-forth before locking anything in.
- **Devices**: one iPhone, one Pixel. Different time zones, different working
  hours.
- **Pains**:
  - Async-only collaboration; they need it to feel like both people built
    the trip together even though they were never online at the same time.
  - "Who paid for what" arguments after the trip.
  - Want to **save things they're considering** without committing them to the
    itinerary yet ("maybe list").
- **Wins**:
  - Robust offline: edit on the train, sync when they're home.
  - A clear "considering" vs "planned" vs "booked" lifecycle.
  - Per-item attribution so they can see who added what.
- **Quote**: "We don't need anything fancy. We need it to not lose our edits
  when one of us is on the Tube."

---

## Persona 3 — "Alex, the Solo Adventurer / Link Hoarder" (Secondary)

- **Age / context**: 24, freelance designer, lives out of a backpack
  4–6 months a year.
- **Trip pattern**: long, loose, frequently re-routed. 30–60 days, dozens of
  cities.
- **Devices**: Android (Pixel 8). Heavy TikTok, Instagram, and YouTube user
  for trip inspiration.
- **Pains**:
  - Hundreds of saved TikToks and IG Reels with no way to surface them on a
    map of where they're useful.
  - Has to retype every cool spot manually into Notes.
  - On the road with patchy 5G; needs everything to **work offline**.
- **Wins**:
  - **Link-paste smart extraction** from social platforms is genuinely magical
    for this persona — possibly the single most important feature.
  - Map-first view to discover gaps in their plan ("oh I didn't realize that
    cafe is 30 minutes off route").
  - Native share-sheet integration on Android so they can share directly from
    TikTok / IG without leaving the source app.
- **Quote**: "I see something cool on TikTok and want to add it to my Lisbon
  list in two taps. That doesn't exist today."

---

## Persona 4 — "Diane, the Business Traveler with Personal Add-ons" (Tertiary)

- **Age / context**: 41, management consultant, in airports weekly.
- **Trip pattern**: weekly work trips + 4–6 personal trips a year.
- **Devices**: iPhone (work-managed). Outlook + Concur required for work.
- **Tools today**: TripIt Pro (paid). Loves the email forwarding inbox.
- **Pains**:
  - When she tacks a personal weekend onto a work trip, the two trips collide
    in TripIt and it's not collaborative with her partner.
  - The "real-time map of my flight" features are nice but the planning UI
    feels dated.
- **Wins**:
  - **Post-MVP** email forwarding inbox so she can switch from TripIt.
  - Clean separation of work segments (private) from personal segments
    (shared with partner).
- **Quote**: "I will pay for this. I'm already paying TripIt $49 a year for
  something that hasn't meaningfully improved in a decade."
- **Note**: Diane's must-have features (email inbox parsing) are explicitly
  **post-MVP**, so we are not designing the MVP for her — but every product
  decision should consider whether it forecloses serving her later.

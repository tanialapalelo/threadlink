# ThreadLink: Architecture

Status: Recommended, not locked. This reflects the PRD's "Recommended Tech Stack" (Section 3.2). Revisit once the prototype (PRD Section 8, step 3) is validated with real pairs.

## Stack

| Layer | Technology | Role |
|---|---|---|
| Mobile (iOS + Android) | React Native (Expo) | Single codebase for both mobile platforms; fast iteration; supports native Share Extension / Share Sheet integration |
| Web | Next.js, sharing React components with mobile | Read/browse-only client in MVP; shared component logic where feasible |
| Backend | Supabase (Postgres + Auth + Realtime) | Data storage, authentication, and realtime feed updates without building custom infra |
| Push notifications | Expo Push / Firebase (FCM + APNs) | Single service path covering both iOS and Android push |
| Link metadata | Self-hosted Open Graph scraper | Fetches title, thumbnail, and OG data from an arbitrary URL server-side |

## System boundaries

- Mobile app (Expo/React Native) owns: UI, iOS Share Extension target, Android Share Sheet intent handling, push notification registration and display.
- Web app (Next.js) owns: read-only feed UI, auth sign-in, no share/compose UI in MVP.
- Supabase owns: all persistent data (pairs, links, notes, tags, bookmarks, notification preferences), auth/session state, and realtime change feed to connected clients.
- Metadata service owns: fetching and normalizing Open Graph data for a submitted URL. This is the only component that makes outbound requests to arbitrary third-party URLs.

## Storage model

- Postgres (via Supabase) is the single source of truth: pairs, pair members, links (with fetched metadata), sender/receiver notes, tags, tag-to-link associations, bookmarks, notification preferences. No client-side-only state that isn't a cache of this data.
- Realtime: feed updates (new link, new note) are pushed to both pair members via Supabase Realtime subscriptions, not polling.
- No file storage layer in MVP: link thumbnails are hotlinked from the source's Open Graph image URL, not re-hosted.

## Auth and access model

- Sign in with Google or Apple only (per PRD F-01), no email/password in MVP.
- A user joins a pair space via a unique invite link or short code; joining requires no separate confirmation step.
- Every data row (link, note, tag, bookmark) belongs to exactly one pair; access control is enforced by pair membership, not by individual user ownership. Both members of a pair can see everything in that pair's space.

## Invariants

Rules this codebase must never violate:

1. A pair space has exactly two members. Group spaces (3+ people) are explicitly out of scope for MVP; do not build the data model in a way that assumes N members.
2. The web app is read-only in MVP. No link sharing, note-adding, or any write action ships on web; only iOS and Android can create content.
3. No in-app media playback. Opening a link always hands off to the native app or system browser; ThreadLink never embeds a video/audio player.
4. No monetization or billing code until the business model is validated post-launch (PRD Section 4.1 recommendation: launch free with no limits, revisit at the 3-month mark).
5. Link metadata fetch failures must degrade gracefully. If the Open Graph scrape fails, fall back to showing the URL's domain and favicon; never render a broken or empty card.
6. Notifications must respect quiet hours and per-type preferences from day one. Do not ship a notification path that bypasses user-configured quiet hours.
7. The iOS Share Extension is the highest-priority piece of the mobile app. Any change that risks breaking "share directly from another app without opening ThreadLink first" is a P0 regression.

## AI / background tasks

None in MVP. AI recommendations and link suggestions are explicitly out of scope (PRD Section 7).

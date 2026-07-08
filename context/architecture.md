# ThreadLink: Architecture

Status: Recommended, not locked. This deviates from the PRD's "Recommended Tech Stack" (Section 3.2) on the backend: the team chose a self-built Go API over Supabase for language familiarity (decided 2026-07-08). It also deviates from the PRD's scope for web, which specified read-only: web now has full write parity with mobile except for OS share-sheet handoff, which browsers don't support (decided 2026-07-08, see Invariant 2). Everything else still follows the PRD recommendation. Revisit once the prototype (PRD Section 8, step 3) is validated with real pairs. The security implications of web's expanded write surface (CSRF, browser session handling) have not been designed yet; this is a known gap to close before web write endpoints are implemented.

## Stack

| Layer | Technology | Role |
|---|---|---|
| Mobile (iOS + Android) | React Native (Expo) | Single codebase for both mobile platforms; fast iteration; supports native Share Extension / Share Sheet integration |
| Web | Next.js, sharing React components with mobile | Full write-parity client (pair creation/invite, manual link paste, notes, tags, bookmarks); shared component logic where feasible. No OS share-sheet equivalent, browsers don't expose one. |
| Backend | Go (net/http or a thin router such as chi/echo) | Single service owning all business logic, auth verification, and data access. Chosen over Supabase because the team is more comfortable writing Go than configuring a platform. |
| Database | PostgreSQL. Docker Compose for local development, a managed provider (for example Neon, Railway, or RDS, exact choice deferred to deploy time) in production | Single source of truth. No Supabase involved anywhere in the stack. |
| Auth | Google/Apple ID token verified directly in Go, custom-issued JWT (access + refresh) sent via `Authorization: Bearer` header | No third-party auth-as-a-service. Google and Apple already handle the login UI and identity proof; Go only verifies their token and issues its own session. |
| Realtime | Polling: client calls `GET /feed` on an interval while the app is foregrounded | Simpler than a WebSocket server, no connection lifecycle to manage. Push notifications (below) cover the background/instant case. Revisit only if polling proves too slow in practice. |
| Push notifications | Expo Push / Firebase (FCM + APNs) | Single service path covering both iOS and Android push. Unaffected by the backend language choice. |
| Link metadata | Self-hosted Open Graph scraper, part of the Go API | Fetches title, thumbnail, and OG data from an arbitrary URL server-side. Because it fetches user-submitted URLs, it is also the component with the highest SSRF risk, see Invariant 10. |

## System boundaries

- Mobile app (Expo/React Native) owns: UI, iOS Share Extension target, Android Share Sheet intent handling, push notification registration and display, secure token storage (Expo SecureStore).
- Web app (Next.js) owns: feed UI, auth sign-in, share/compose UI (manual link paste, sender/receiver notes, tags, bookmarks), in-memory (not localStorage) token storage for the session. Same write capability as mobile except OS share-sheet handoff, which has no browser equivalent.
- Go API service owns: all business logic, auth token verification and session issuance, all database access, the feed endpoint clients poll, and the metadata-scraping endpoint. It is the only component that talks to Postgres and the only component that makes outbound requests to arbitrary third-party URLs.
- No client (mobile or web) ever queries Postgres directly. All data access goes through the Go API.

## Storage model

- PostgreSQL is the single source of truth: pairs, pair members, links (with fetched metadata), sender/receiver notes, tags, tag-to-link associations, bookmarks, notification preferences. No client-side-only state that isn't a cache of this data.
- Realtime: feed updates are read via polling (see Stack table above), not pushed by the database.
- No file storage layer in MVP: link thumbnails are hotlinked from the source's Open Graph image URL, not re-hosted.

## Auth and access model

- Sign in with Google or Apple only (per PRD F-01), no email/password in MVP.
- Flow: client obtains an ID token from the Google/Apple SDK, sends it to the Go API, Go verifies the token's signature against Google's/Apple's published public keys (JWKS) and checks its `aud` (matches this app's client ID), `iss`, and `exp` claims. Only after that verification does Go issue its own short-lived access JWT plus a longer-lived refresh token.
- Access tokens are short-lived (target: 15 minutes) so a leaked token has a small window of use. Refresh tokens are longer-lived but are stored server-side (hashed, not plaintext) so they can be individually revoked, for example on logout or if a device is compromised, which a pure stateless JWT cannot do without this record.
- A user joins a pair space via a unique invite link or short code; joining requires no separate confirmation step.
- Every data row (link, note, tag, bookmark) belongs to exactly one pair; access control is enforced by pair membership, not by individual user ownership. Both members of a pair can see everything in that pair's space. Because this project does not use Supabase Row Level Security or any database-level policy engine, this check has no safety net below the application layer, see Invariant 8 below.

## Invariants

Rules this codebase must never violate:

1. A pair space has exactly two members. Group spaces (3+ people) are explicitly out of scope for MVP; do not build the data model in a way that assumes N members.
2. The web app has full write parity with mobile (pair creation/invite, manual link paste, sender/receiver notes, tags, bookmarks), decided 2026-07-08, deviating from the original PRD-specified read-only web scope. The only capability web cannot replicate is native OS share-sheet handoff, since browsers have no equivalent; web sharing is manual-paste only. Every API handler must therefore treat mobile and web clients identically for authorization purposes (see Invariant 8); nothing may gate a write action on client platform. The security review for this expanded write surface (CSRF protection, browser session/token handling) has not been done yet and must happen before any web write endpoint ships.
3. No in-app media playback. Opening a link always hands off to the native app or system browser; ThreadLink never embeds a video/audio player.
4. No monetization or billing code until the business model is validated post-launch (PRD Section 4.1 recommendation: launch free with no limits, revisit at the 3-month mark).
5. Link metadata fetch failures must degrade gracefully. If the Open Graph scrape fails, fall back to showing the URL's domain and favicon; never render a broken or empty card.
6. Notifications must respect quiet hours and per-type preferences from day one. Do not ship a notification path that bypasses user-configured quiet hours.
7. The iOS Share Extension is the highest-priority piece of the mobile app. Any change that risks breaking "share directly from another app without opening ThreadLink first" is a P0 regression.
8. Every API handler that touches pair-scoped data must explicitly verify the requesting user is a member of that pair before reading or writing anything. There is no Row Level Security or database policy engine enforcing this as a fallback, so a missed check is a real cross-pair data leak. Implement this as one shared middleware/helper used by every such handler, not a check repeated ad hoc per handler, so it cannot be forgotten in a new one.
9. All database queries use parameterized statements (via the standard library's `database/sql` placeholders or a query builder), never string-concatenated SQL. The application connects to Postgres with a least-privilege role, not a superuser.
10. The Open Graph metadata scraper must never fetch localhost, link-local, or private IP ranges (including cloud metadata endpoints such as `169.254.169.254`), must not follow a redirect into those ranges, must enforce a request timeout, and must cap the response body size it reads. This is a Server-Side Request Forgery (SSRF) boundary: the scraper fetches arbitrary user-submitted URLs, so it is the component most exposed to a malicious input.
11. Secrets (database connection string, JWT signing key, Google/Apple OAuth client credentials) are read from environment variables and never committed to git. `.env*` is already covered by `.gitignore`.
12. The Go API's CORS policy allows only known origins (the web app's domain), never a wildcard `*`, since the API accepts an `Authorization` header that a permissive CORS policy could expose to an unrelated origin.
13. All traffic to the Go API is over HTTPS in every environment except local development. A bearer JWT sent over plain HTTP is readable to anyone on the network path.

## AI / background tasks

None in MVP. AI recommendations and link suggestions are explicitly out of scope (PRD Section 7).

## Git workflow

Strategy: GitHub Flow. `main` is always deployable.

- Each implementation unit (one superpowers plan) gets its own short-lived branch, named `feat/<unit-slug>` (or `fix/...`, `chore/...` matching Conventional Commits type).
- Merge to `main` only after the unit's task review (and, once tests exist, after they pass).
- Delete the branch after merge.
- Commit messages follow Conventional Commits (feat/fix/docs/chore/refactor/test).
- Commit messages never include a `Co-Authored-By: Claude` (or any AI attribution) trailer.
- No GitFlow-style develop/release/hotfix branches until there is a real multi-environment release cadence to justify them.

# Progress Tracker

## Current Phase
UI design system decided and documented (mobile + web). Web's scope changed from read-only to full write parity with mobile (decided 2026-07-08); `architecture.md` and `project-overview.md` are updated to reflect this, but the security review for web's expanded write surface is still outstanding.

## Active Plan
None yet. Two things are open and unfinished:
1. Unit "monorepo skeleton" (apps/web, apps/mobile placeholder, apps/api in Go): design was discussed in chat and approved in principle, but the design doc was never written to docs/superpowers/specs/. Paused mid-brainstorm on the Go router choice (net/http vs chi) to prioritize UI/Figma work; not started as an actual implementation plan.
2. Security review for web's new write capability (CSRF protection, browser session/token handling) per architecture.md's Status note and Invariant 2. Must happen before any web write endpoint is implemented.

## Last Completed Unit
Web scope change formalized: `context/architecture.md` (Status note, Stack table, System boundaries, Invariant 2) and `context/project-overview.md` (In scope, Success criteria) updated so web has full write parity with mobile except OS share-sheet handoff. Mockups for both mobile (Feed, Onboarding/invite, Add link+note, Bookmarks) and web (Feed with sidebar nav + centered modal, Bookmarks) were built and saved locally as standalone HTML in `docs/superpowers/specs/mockups/`, since the Figma MCP connection hit its Starter-plan rate limit after only 2 of the 4 mobile screens (Feed, Onboarding) were pushed to the Figma file (https://www.figma.com/design/4SpPSCo1KhEuYIPYNvjEmX).

## Next Up
1. Once the Figma MCP rate limit resets (or the plan is upgraded), push the remaining screens (mobile Add link+note, mobile Bookmarks, web Feed, web Bookmarks) to the same Figma file using the saved local HTML mockups as the source of truth.
2. Add the web layout patterns (sidebar nav, centered modal, single-column feed) decided this session to `context/ui-context.md`, which currently only documents mobile layout patterns.
3. Security review for web's write capability (see Active Plan above), before any web write endpoint is implemented.
4. Resume the Go monorepo skeleton design doc (see Active Plan above), including the still-open net/http vs chi router decision. The user is learning Go in parallel, so actual Go implementation is intentionally deferred, docs/specs for it can proceed earlier if useful.

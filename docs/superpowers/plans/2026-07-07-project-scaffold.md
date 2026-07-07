# ThreadLink Project Scaffold Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create the CLAUDE.md entry point, five context files, and `.claude/settings.json` described in `docs/superpowers/specs/2026-07-07-project-scaffold-design.md`, so ThreadLink has a working documentation and permissions scaffold before any product code is written.

**Architecture:** Seven independent static files (five markdown context files, one root CLAUDE.md, one JSON settings file). No code, no runtime, no dependencies between files at write time. Each task creates exactly one file and verifies its content with a grep or JSON-parse check, since there is no compiled code to run.

**Tech Stack:** Markdown, JSON. No frameworks.

## Global Constraints

- This project is not yet a git repository (confirmed: no `.git` directory). Do not run `git init`, `git add`, or `git commit` as part of this plan. Every task ends with a verification step instead of a commit step.
- No em dash character ("-" used instead, or sentence restructured) in any file content written by this plan.
- Do not invent content for `context/code-standards.md` or `context/ui-context.md` beyond the placeholder/blank structure specified below. Real content for those two files is explicitly out of scope for this plan.

---

### Task 1: context/project-overview.md

**Files:**
- Create: `context/project-overview.md`

**Interfaces:**
- Consumes: content from `ThreadLink_PRD.pdf` (already read, transcribed below; no re-parsing needed).
- Produces: `context/project-overview.md`, referenced by name from `CLAUDE.md` in Task 6.

- [ ] **Step 1: Write the file**

Write this exact content to `context/project-overview.md`:

```markdown
# ThreadLink: Project Overview

## What this is
ThreadLink is a shared link inbox for two people ("pairs") who want to send each other links (YouTube videos, Instagram Reels, articles, podcasts) with context, and revisit them intentionally instead of losing them in chat history.

## Goals
1. Replace informal link-sharing in WhatsApp/Telegram with a structured, revisitable shared inbox.
2. Make sharing as frictionless as sending a message (native share sheet, no app-switching required).
3. Let both people in a pair leave context (notes) on a shared link so intent isn't lost.
4. Make revisiting intentional via a filterable, searchable feed instead of scrolling chat history.
5. Validate retention and engagement before introducing any monetization.

## Target users
- Couples: share entertainment content (Netflix picks, travel inspiration, music) as a shared wish list. One partner is usually more active in discovering content.
- Creative collaborators: two people on a shared project (designers, writers, indie founders) using it as a living, taggable reference board.
- Best friends (future, post-launch): casual sharing, lower priority than the two segments above.

## Core user flow
1. User A creates a named pair space (for example "You & Ardi") and invites User B via a shareable link or short code.
2. User B opens the invite link and joins instantly. No account creation friction, no email confirmation, sign in with Google or Apple.
3. User A shares a link into the space, either by pasting a URL in-app, or via the native OS share sheet from another app (YouTube, Instagram, etc.).
4. The app auto-fetches metadata (title, thumbnail, content type, domain) so the link renders as a rich card.
5. User A optionally adds a sender note, for example "start at 4:30, the data viz part is insane".
6. User B gets a push notification with the sender's name and the content title.
7. User B opens the link (in the native app or browser, not in-app playback), then optionally adds a receiver note.
8. Both users can filter the feed (by sender, content type, tag, unseen, bookmarked) and search it to find what they meant to revisit.

## Features: Phase 1 (MVP)
- Pair creation and onboarding: named pair space, shareable invite link or short code, instant join, max 3-step onboarding.
- Link sharing: paste URL or share from OS share sheet (iOS Share Extension, Android Share Sheet, web share button); auto-detect content type (video, reel, podcast, article, image); auto-fetch title, thumbnail, duration, domain.
- Notes and annotations: optional sender note at share time; separate receiver note after viewing; both attributed by name/avatar and color-coded per person; plain text only in MVP.
- Push notifications: on new link, on new note; shows sender name, content title, and source; quiet hours; per-type notification preferences (links vs. notes).
- Shared feed and filtering: chronological feed, filter by sender/content type/tag, "unseen" indicator, "bookmarked" filter, search by title/note/domain.
- Bookmarks and tags: one-tap bookmark into a dedicated Bookmarks view; freeform tags created at share time or after, with suggestions from past usage; tags visible to both users.

## In scope (Phase 1)
Everything listed under "Features: Phase 1 (MVP)" above, across iOS (full feature set plus Share Extension), Android (full feature set plus Share Sheet), and web (read/browse only, mobile-first responsive, no sharing from web).

## Out of scope (MVP)
- Group spaces (3+ people): a space is always exactly one pair.
- In-app media playback: links always open in the native app or browser (YouTube app, Safari, etc.), never rendered inside ThreadLink.
- AI recommendations or link suggestions.
- Public profiles or discovery features.
- Full chat or messaging system (Phase 2 may add a 3-5 reply thread per link, not a general chat).
- Link expiration or auto-deletion.
- Monetization or paywalls of any kind: no billing code until the business model is validated post-launch.

## Phase 2 (post-launch, not being built now)
Light discussion thread per link (3-5 replies max), weekly digest email, auto-archive of links older than 90 days with no interaction, folder/project grouping, emoji reactions, iMessage/WhatsApp share extension.

## Success criteria (from PRD success metrics)
- 3-month target: 500 active pairs (both users active in 7 days), 5+ links shared per pair per week, 40% D30 retention (both users), 0.4 avg notes per shared link, 35% notification open rate.
- 6-month target: 2,500 active pairs, 8+ links shared per pair per week, 55% D30 retention, 0.7 avg notes per shared link, 40% notification open rate.
- Concretely, "done" for Phase 1 means: a signed-in pair can invite each other, share a link from a native share sheet with a note, get notified, view the link, add their own note, and find it again later via filter or search, on iOS, Android, and web (read-only).

## Source
Derived from `ThreadLink_PRD.pdf` v1.0 (May 2026, Draft, In Review).
```

- [ ] **Step 2: Verify**

Run: `grep -c "^## " context/project-overview.md`
Expected: `10` (What this is, Goals, Target users, Core user flow, Features: Phase 1, In scope, Out of scope, Phase 2, Success criteria, Source)

Run: `grep -n "—" context/project-overview.md`
Expected: no output (no em dash present)

---

### Task 2: context/architecture.md

**Files:**
- Create: `context/architecture.md`

**Interfaces:**
- Consumes: PRD section 3.2 "Recommended Tech Stack" and section 6 "Risks & Mitigations" and section 7 "Out of Scope" (already transcribed below).
- Produces: `context/architecture.md`, referenced by name from `CLAUDE.md` in Task 6.

- [ ] **Step 1: Write the file**

Write this exact content to `context/architecture.md`:

```markdown
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
```

- [ ] **Step 2: Verify**

Run: `grep -c "^[0-9]\." context/architecture.md`
Expected: `7` (seven numbered invariants)

Run: `grep -n "—" context/architecture.md`
Expected: no output

---

### Task 3: context/code-standards.md

**Files:**
- Create: `context/code-standards.md`

**Interfaces:**
- Consumes: nothing (placeholder file by design, see spec).
- Produces: `context/code-standards.md`, referenced by name from `CLAUDE.md` in Task 6.

- [ ] **Step 1: Write the file**

Write this exact content to `context/code-standards.md`:

```markdown
# ThreadLink: Code Standards

Status: placeholder. No code has been written yet. This file gets filled in during the first implementation unit (project scaffolding for Expo/Next.js/Supabase) with concrete conventions. Do not invent conventions speculatively before that unit exists.

## What will go here once implementation starts
- TypeScript conventions (strict mode on/off, type vs. interface usage)
- React Native / Expo file and folder structure
- Next.js file and folder structure (App Router vs. Pages Router)
- Supabase client usage patterns (where queries live, how types are generated)
- API/data-layer conventions (query naming, error shape)
- Styling approach (for example NativeWind, StyleSheet, Tailwind for web)
- Import conventions and lint/format tooling

## Current rule
Until this file has real content, follow whatever pattern is established by the first unit that touches a given area, and keep it consistent within that area. Update this file in the same unit that establishes a new pattern.
```

- [ ] **Step 2: Verify**

Run: `grep -c "placeholder" context/code-standards.md`
Expected: `1` or greater (confirms the placeholder status line is present)

---

### Task 4: context/ui-context.md

**Files:**
- Create: `context/ui-context.md`

**Interfaces:**
- Consumes: nothing (blank template by design, see spec).
- Produces: `context/ui-context.md`, referenced by name from `CLAUDE.md` in Task 6.

- [ ] **Step 1: Write the file**

Write this exact content to `context/ui-context.md`:

```markdown
# ThreadLink: UI Context

Status: blank template. No visual design has been discussed yet. Fill this in during a dedicated brainstorming session once a visual direction is chosen. Do not let the agent invent colors, type scales, or component conventions before that session happens.

## Color tokens
(semantic token name to hex value, once decided)

## Typography
(typeface, scale, weights)

## Border radius scale
(named scale, for example sm/md/lg to px values)

## Component library
(which library, for example NativeWind plus custom, or a specific design system)

## Layout patterns
(navigation structure, modal patterns, feed card layout, empty states)

## Icon library
(which icon set, usage rules)
```

- [ ] **Step 2: Verify**

Run: `grep -c "^## " context/ui-context.md`
Expected: `6`

---

### Task 5: context/progress-tracker.md

**Files:**
- Create: `context/progress-tracker.md`

**Interfaces:**
- Consumes: this plan's own file path, to fill the "Active Plan" field.
- Produces: `context/progress-tracker.md`, referenced by name from `CLAUDE.md` in Task 6.

- [ ] **Step 1: Write the file**

Write this exact content to `context/progress-tracker.md`:

```markdown
# Progress Tracker

## Current Phase
Project scaffold setup: context files, CLAUDE.md, and permissions, no product code yet.

## Active Plan
docs/superpowers/plans/2026-07-07-project-scaffold.md

## Last Completed Unit
None yet.

## Next Up
Fill in context/code-standards.md once the first implementation unit (Expo/Next.js/Supabase project scaffolding) begins.
```

- [ ] **Step 2: Verify**

Run: `grep -c "^## " context/progress-tracker.md`
Expected: `4`

---

### Task 6: CLAUDE.md

**Files:**
- Create: `CLAUDE.md`

**Interfaces:**
- Consumes: file names from Tasks 1-5 (`context/project-overview.md`, `context/architecture.md`, `context/ui-context.md`, `context/code-standards.md`, `context/progress-tracker.md`). All five must already exist for the references to be accurate, so this task runs after Tasks 1-5.
- Produces: `CLAUDE.md`, the entry point read at the start of every session.

- [ ] **Step 1: Write the file**

Write this exact content to `CLAUDE.md`:

```markdown
# ThreadLink

## Read before implementing or making any architectural decision
1. `context/project-overview.md`: product definition, goals, features, scope
2. `context/architecture.md`: stack, system boundaries, storage model, invariants
3. `context/ui-context.md`: visual language and component conventions (blank until a design session happens)
4. `context/code-standards.md`: implementation conventions (placeholder until first code unit)
5. `context/progress-tracker.md`: current phase and what's next

## Process
Use the superpowers skill set for how work gets done in this repo:
- superpowers:brainstorming before designing any new feature
- superpowers:writing-plans plus superpowers:executing-plans (or subagent-driven-development) to implement
- superpowers:test-driven-development for any code with test coverage
- superpowers:systematic-debugging when something breaks
- superpowers:verification-before-completion before declaring a unit done

Do not write ad hoc workflow rules elsewhere. These skills are the source of truth for process.

## After each unit of work
Update `context/progress-tracker.md`'s four fields (Current Phase, Active Plan, Last Completed Unit, Next Up). If implementation changes the architecture, scope, or standards documented in the context files, update the relevant file before continuing.

## Commands
None yet. No project has been scaffolded.
```

- [ ] **Step 2: Verify**

Run: `test -f context/project-overview.md && test -f context/architecture.md && test -f context/ui-context.md && test -f context/code-standards.md && test -f context/progress-tracker.md && echo ALL_EXIST`
Expected: `ALL_EXIST`

Run: `wc -l < CLAUDE.md`
Expected: a number under 200

---

### Task 7: .claude/settings.json

**Files:**
- Create: `.claude/settings.json`

**Interfaces:**
- Consumes: nothing.
- Produces: `.claude/settings.json`, read by Claude Code at session start to determine tool permissions.

- [ ] **Step 1: Write the file**

Write this exact content to `.claude/settings.json`:

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": [
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Read",
      "Write",
      "Edit"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Read(./.env)",
      "Read(./.env.*)"
    ]
  }
}
```

- [ ] **Step 2: Verify the JSON is valid**

Run: `python3 -m json.tool .claude/settings.json`
Expected: the same JSON, pretty-printed, with no error

- [ ] **Step 3: Verify git commit/push and gh are not in the allow list**

Run: `grep -i "commit\|push\|gh(" .claude/settings.json`
Expected: no output (confirms none of these are auto-allowed)

---

## Final check

- [ ] **Step: Confirm all seven files exist and no em dash slipped into any of them**

Run: `grep -rl "—" CLAUDE.md context/ .claude/settings.json 2>/dev/null; echo DONE`
Expected: `DONE` with no filenames printed before it (empty grep result means clean)

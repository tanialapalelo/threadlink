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
Everything listed under "Features: Phase 1 (MVP)" above, across iOS (full feature set plus Share Extension), Android (full feature set plus Share Sheet), and web (full feature set: pair creation/invite, pasting a link manually, sender/receiver notes, tags, and bookmarks, decided 2026-07-08, see architecture.md for the deviation from the original PRD-specified read-only web scope). Web has no equivalent to the OS share sheet since browsers don't expose one, so link sharing on web is manual-paste only; every other write action mirrors mobile.

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
- Concretely, "done" for Phase 1 means: a signed-in pair can invite each other, share a link (native share sheet on mobile, manual paste on web) with a note, get notified, view the link, add their own note, and find it again later via filter or search, on iOS, Android, and web.

## Source
Derived from `ThreadLink_PRD.pdf` v1.0 (May 2026, Draft, In Review).

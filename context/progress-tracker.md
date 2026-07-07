# Progress Tracker

## Current Phase
Paused mid-brainstorm on the first implementation unit, switching focus to UI design (Figma) before writing any app code.

## Active Plan
None yet. Two brainstorms are open and unfinished:
1. Unit "monorepo skeleton" (apps/web, apps/mobile placeholder, apps/api in Go): design was discussed in chat and approved in principle, but the design doc was never written to docs/superpowers/specs/. Not started as an actual implementation plan.
2. UI design system (context/ui-context.md): just starting, blocked on Figma MCP access (see below).

## Last Completed Unit
context/architecture.md updated to pivot the backend from Supabase to a self-built Go API (net/http stdlib, JWT auth via header, polling-based realtime), with security invariants added (8-13). Committed in 401d459.

## Next Up
1. User is restarting the Claude Code session so the already-connected Figma Dev Mode MCP server's tools load (they were connected mid-session and did not appear in this session's tool list).
2. Once Figma access works, brainstorm context/ui-context.md from the actual Figma file.
3. The Go monorepo skeleton unit (see Active Plan above) is still open and needs its design doc written before implementation, whenever backend work resumes. The user is learning Go in parallel, so actual Go implementation is intentionally deferred, docs/specs for it can proceed earlier if useful.

# ThreadLink Project Scaffold: Design

Date: 2026-07-07
Status: Approved

## Goal

Set up ThreadLink's Claude Code working environment before any product code is written: an entry-point `CLAUDE.md`, a set of persistent project-truth docs (adapted from the Six-File Context Methodology), a lightweight progress pointer, and `.claude/settings.json` permissions. All of it wired to run through the `superpowers` skill set for process (brainstorming, writing-plans, executing-plans, TDD, systematic-debugging, verification-before-completion) rather than duplicating that discipline in prose.

## Why this combination

Three sources were compared:

- **Anatomy of the `.claude` folder**: infrastructure layer, `CLAUDE.md`, `.claude/settings.json` (permissions/hooks), `skills/`, `agents/`.
- **Six-File Context Methodology**: content layer, `project-overview.md`, `architecture.md`, `code-standards.md`, `ai-workflow-rules.md`, `ui-context.md`, `progress-tracker.md`, plus spec-per-unit build planning.
- **Superpowers**: process layer, skills that already systematize what Six-File's manual prompts describe (brainstorming is basically Six-File's "planning AI conversation"; writing-plans/executing-plans is basically Six-File's build-plan plus spec-per-unit; verification-before-completion is basically Six-File's "Verify when done" checklist).

Since superpowers already operationalizes Six-File's *process* prompts as skills, only Six-File's *content* files are adopted, minus the one file (`ai-workflow-rules.md`) that would duplicate superpowers' own skill instructions.

## Folder structure

```
threadlink/
├── CLAUDE.md
├── .claude/
│   └── settings.json
├── context/
│   ├── project-overview.md
│   ├── architecture.md
│   ├── code-standards.md
│   ├── ui-context.md
│   └── progress-tracker.md
├── docs/superpowers/
│   ├── specs/
│   └── plans/
└── ThreadLink_PRD.pdf
```

`ai-workflow-rules.md` is intentionally omitted. CLAUDE.md points to the superpowers skills instead.

## File-by-file content plan

**`CLAUDE.md`**: short entry point (well under 200 lines). Lists the read order for `context/*.md`, instructs the agent to use superpowers skills for process (brainstorm before features, writing-plans/executing-plans for implementation, TDD, systematic-debugging, verification-before-completion), and instructs the agent to update `context/progress-tracker.md`'s pointer fields after each unit of work.

**`context/project-overview.md`**: filled now, sourced directly from the PRD, covering problem statement, vision, target users, success metrics, Phase 1 feature list, out-of-scope list, success criteria. This is authoritative product intent, so ambiguous requirements resolve against this file, not agent guesses.

**`context/architecture.md`**: drafted now from the PRD's "Recommended Tech Stack" table (Expo/React Native, Next.js web, Supabase, Expo Push/FCM+APNs, self-hosted OG scraper), explicitly marked as a recommendation pending validation, not a locked decision. Includes an **Invariants** section derived from the PRD's Risks and Out-of-Scope sections (minimum four rules), for example:
- Web app is read-only in MVP, no sharing/posting from web.
- No monetization/paywall code until business model is validated post-launch.
- Link metadata fetch failures must fall back to domain and favicon, never render a broken card.
- No group spaces (3+ people), the data model assumes exactly one pair per space.

**`context/code-standards.md`**: left minimal/placeholder. No code exists yet, so concrete conventions (folder layout, naming, lint rules) will be filled in during the first implementation unit rather than invented speculatively now.

**`context/ui-context.md`**: left as a blank template (structure only: color tokens, typography, border radius, component conventions, layout patterns). Filled in a dedicated future brainstorming session once a visual direction is chosen, since no visual design has been discussed yet.

**`context/progress-tracker.md`**: thin pointer only, four fields, no narrative duplication of what plan docs already hold:

```markdown
# Progress Tracker

## Current Phase
[current phase]

## Active Plan
[link to docs/superpowers/plans/*.md currently in progress, or "none yet"]

## Last Completed Unit
[...]

## Next Up
[...]
```

**`.claude/settings.json`**:

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": ["Bash(git status)", "Bash(git diff *)", "Bash(git log *)", "Read", "Write", "Edit"],
    "deny": ["Bash(rm -rf *)", "Read(./.env)", "Read(./.env.*)"]
  }
}
```

`git commit`, `git push`, and every `gh` subcommand (including `gh pr comment` / `gh issue comment`) are deliberately absent from `allow` so they always prompt for confirmation. This directly addresses the requirement that commits and GitHub comments never happen without explicit user approval.

## Git

The project is not yet a git repository. Git `init` is deferred until the user decides to start one; this design doc and the scaffold files are written to the working tree without a commit step.

## Out of scope for this scaffold

- Actual `git init` / remote setup.
- Filling `code-standards.md` and `ui-context.md` with real content (deferred to later sessions, as noted above).
- Any product code (mobile app, web app, Supabase schema).

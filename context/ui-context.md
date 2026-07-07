# ThreadLink: UI Context

Status: Decided during a brainstorming session on 2026-07-08 (see `docs/superpowers/specs/2026-07-08-ui-design-system-design.md` for the full rationale and options considered). Mood direction: Clean & Minimal as the base, with warmth introduced through accent color rather than an overall "romantic" theme, so the app reads as a shared utility both couples and creative collaborators are comfortable in.

## Color tokens

| Token | Hex | Usage |
|---|---|---|
| `bg` | `#FAFAF8` | App background (warm off-white, not stark white) |
| `surface` | `#FFFFFF` | Cards, sheets, elevated surfaces |
| `border` | `#EFEFEF` | Card borders, dividers |
| `text` | `#1A1A1A` | Primary text |
| `text-muted` | `#8A8578` | Secondary text, timestamps, metadata (warm gray, not cool gray) |
| `person-a` | `#C4703F` | Terracotta. Default accent color for the first/inviting member of a pair; used for their avatar ring, their notes, and as the app's default accent (buttons, links, active states) before a second member joins |
| `person-b` | `#7A8B69` | Sage green. Assigned to the second member of a pair on join, for their avatar ring and their notes |

Per-person colors exist because notes are attributed by color per Invariant/feature spec (sender vs. receiver notes). Every pair has exactly two members (architecture.md Invariant 1), so exactly two person colors are needed, not a larger rotating palette.

## Typography

Typeface: Inter (system sans-serif fallback: `-apple-system`, `Helvetica Neue`, `Arial`, sans-serif).

| Style | Size / Weight | Usage |
|---|---|---|
| Headline | 24px / 700 | Screen titles |
| Title | 17px / 600 | Card titles, link titles |
| Body | 15px / 400 | Body text, notes |
| Caption | 13px / 400 | Timestamps, metadata, secondary labels (use `text-muted`) |

## Border radius scale

| Token | Value | Usage |
|---|---|---|
| `sm` | 8px | Small elements: thumbnails, inputs |
| `md` | 12px | Cards, bottom sheets |
| `lg` | 16px | Larger containers, modals |
| `pill` | 999px | Buttons, tags, chips |

## Component library

NativeWind (Tailwind CSS syntax) plus custom-built components, shared where feasible between the Expo (React Native) mobile app and the Next.js web app. No third-party component kit (for example Tamagui or gluestack-ui): custom components give full control over the tokens above and keep both platforms visually identical without adopting another library's defaults.

## Layout patterns

- **Mobile navigation**: bottom tab bar with three tabs: Feed, Bookmarks, Profile/Pair settings. No floating action button; sharing a link happens via the OS share sheet (Invariant 7 in architecture.md) or a compose action inside the Feed tab, not a persistent FAB.
- **Modals / short actions**: bottom sheet (drag handle, dismissible), used for adding a sender or receiver note and for pasting a link manually. Reserved for short, single-purpose actions; not used for multi-step flows.
- **Feed card layout**: sender's avatar (ringed in their person color) + name + relative timestamp on top; content thumbnail; title below the thumbnail; sender/receiver note beneath the title, rendered in the note author's person color.
- **Empty states**: not yet designed, revisit when the Feed screen is actually built, since the right empty state depends on onboarding flow details not yet specced.

## Icon library

Lucide (`lucide-react-native` on mobile, `lucide-react` on web): single icon set from one source library, kept visually consistent across platforms, actively maintained.

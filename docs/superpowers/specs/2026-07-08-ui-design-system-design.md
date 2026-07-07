# ThreadLink UI Design System: Design

Date: 2026-07-08
Status: Approved

## Goal

Choose a visual direction and a minimal set of design tokens for ThreadLink so that `context/ui-context.md` stops being a blank template and mobile (Expo/React Native) and web (Next.js) implementation can start from a shared, consistent visual language. No Figma file existed yet, so this session established the direction from scratch using the brainstorming skill's visual companion (mood board comparisons rendered in-browser) rather than importing an existing design.

## Approach considered

Four mood directions were presented side by side as feed-card mockups: Warm & Personal, Clean & Minimal, Playful & Expressive, and Editorial & Considered. Given the target users (couples as the primary segment, creative collaborators as a close second, per `context/project-overview.md`), a theme that leaned too far into "romantic" risked alienating the collaborator segment, and a theme that leaned too playful/loud didn't fit either segment's actual use case (an inbox to revisit content intentionally, not a chat app). **Clean & Minimal** was chosen as the structural base, with warmth introduced through accent color rather than through the overall theme, so the app reads as a shared utility both segments are comfortable in.

## Color tokens

| Token | Hex | Usage |
|---|---|---|
| `bg` | `#FAFAF8` | App background (warm off-white, not stark white) |
| `surface` | `#FFFFFF` | Cards, sheets, elevated surfaces |
| `border` | `#EFEFEF` | Card borders, dividers |
| `text` | `#1A1A1A` | Primary text |
| `text-muted` | `#8A8578` | Secondary text, timestamps, metadata (warm gray, not cool gray) |
| `person-a` | `#C4703F` | Terracotta: default accent, and the color assigned to the first/inviting member of a pair |
| `person-b` | `#7A8B69` | Sage green: assigned to the second member of a pair on join |

The accent color went through two rounds of comparison: first a mood-level palette (Coral, Terracotta, Amber, Rose) against the Clean & Minimal base, then a second-person color (Sage green, Mustard, Muted plum) to pair with Terracotta. Terracotta was picked for reading as earthy and warm without feeling exclusively "couple/romantic." Sage green was picked as the pairing color for having clear contrast against terracotta while staying in the same warm-adjacent register, explicitly avoiding cool blues/purples per user preference.

Exactly two person colors exist because every pair has exactly two members (`context/architecture.md` Invariant 1); there is no rotating palette for a third member because a third member can never exist in this data model.

## Typography

Typeface: Inter (system sans-serif fallback stack). Chosen over a rounded sans (Nunito-style) and a sans+serif hybrid for being the safest, most legible option at small mobile sizes, consistent with the "utility, not romance" positioning of the Clean & Minimal direction.

| Style | Size / Weight | Usage |
|---|---|---|
| Headline | 24px / 700 | Screen titles |
| Title | 17px / 600 | Card titles, link titles |
| Body | 15px / 400 | Body text, notes |
| Caption | 13px / 400 | Timestamps, metadata, secondary labels |

## Border radius scale

Soft radius was chosen over Sharp (too technical-feeling) and Round (too casual/pill-like for a utility app):

| Token | Value | Usage |
|---|---|---|
| `sm` | 8px | Small elements: thumbnails, inputs |
| `md` | 12px | Cards, bottom sheets |
| `lg` | 16px | Larger containers, modals |
| `pill` | 999px | Buttons, tags, chips |

## Component library

NativeWind (Tailwind CSS syntax) plus custom-built components, shared where feasible between the Expo mobile app and the Next.js web app.

Two alternatives were considered and rejected: **Tamagui** (cross-platform kit with a compiler) was rejected for adding a learning curve and a risk of reading as generic without heavy customization; **gluestack-ui** (NativeWind-based, unstyled-by-default components) was rejected as an unnecessary middle layer once the team already committed to writing custom components on top of NativeWind directly. Using NativeWind directly keeps one styling mental model across both platforms and full control over the tokens above.

## Icon library

Lucide (`lucide-react-native` on mobile, `lucide-react` on web). Chosen over Phosphor (more visual variants than needed) and Feather (less actively maintained) for being lightweight, consistent across the two platform packages, and actively maintained.

## Layout patterns

- **Mobile navigation**: bottom tab bar with three tabs: Feed, Bookmarks, Profile/Pair settings. Chosen over a single-feed-with-filter-bar (bookmarks would be under-exposed) and over a tab-bar-plus-FAB (a persistent floating share button is redundant once OS share-sheet sharing, architecture.md Invariant 7, is the primary share path).
- **Modals / short actions**: bottom sheet (drag handle, dismissible) for adding a sender/receiver note and for pasting a link manually. Chosen over a full-screen modal, which would feel disproportionately heavy for a single-field action.
- **Feed card layout**: sender's avatar (ringed in their person color) + name + relative timestamp on top; content thumbnail; title below the thumbnail; sender/receiver note beneath the title, rendered in the note author's person color.
- **Empty states**: explicitly deferred. The right empty-state copy/illustration depends on onboarding flow details (`context/project-overview.md`'s "max 3-step onboarding") that haven't been specced yet. Revisit when the Feed screen implementation unit is planned.

## Out of scope for this design

- Any actual Figma file or high-fidelity mockups: this session used the brainstorming skill's HTML-based visual companion, not Figma, since no Figma file existed to start from. A Figma file can be generated from these tokens in a later session if the team wants one before implementation.
- Onboarding flow screens and copy (referenced above but not designed here).
- Web-specific layout (the read-only Next.js client's navigation is not yet decided; only the mobile bottom tab bar was discussed in this session).
- Dark mode: not discussed; `context/ui-context.md` documents light mode only for now.

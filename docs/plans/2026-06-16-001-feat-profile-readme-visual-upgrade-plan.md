---
type: plan
skill: ce-plan
date: 2026-06-16
origin: chat
domain: software
---

# Upgrade GitHub profile README with visual identity and interactivity

## Summary

Transform the `fraction12/fraction12` GitHub profile README from a static, text-heavy repo catalog into a visually distinctive, developer-forward landing page. The upgrade adds a terminal/ASCII-style header, collapsible repo sections, a "now" section, and dynamic GitHub stats widgets. All changes are confined to this repository; one lightweight SVG header asset will be committed so the profile does not depend on external image hosting for its main visual hook.

## Problem Frame

The current README is clean and honest, but it reads like a long spreadsheet. On GitHub, profile READMEs are a first impression — the best ones communicate taste and motion immediately. The current page buries the reader in tables before any personality lands. We need to preserve the non-corporate voice while making the page scannable, memorable, and slightly interactive.

## Requirements

- **R1.** Keep the existing informal, non-corporate voice; do not revert to corporate-speak.
- **R2.** Add a visual header that matches the agent/runtime/tooling theme and renders reliably in both GitHub light and dark themes.
- **R3.** Make the long repo catalog scannable by collapsing sections behind `<details>` blocks.
- **R4.** Add a "now" / currently-building section near the top so the profile feels alive.
- **R5.** Add dynamic GitHub stats widgets for top languages and general profile stats.
- **R6.** Ensure all assets needed for the header are committed to this repo; avoid hot-linking to external image hosts for the primary visual.
- **R7.** Maintain the existing 30-repo catalog (including the soon-to-be-public private repos already listed).
- **R8.** Verify the README renders correctly on GitHub after push.

## Key Technical Decisions

| ID | Decision | Rationale |
| --- | --- | --- |
| **KTD1** | Header will be an ASCII/terminal-style text block inside the README, optionally backed by a committed SVG banner. | Pure ASCII requires zero hosting and renders everywhere. An SVG banner adds polish without raster image fragility. SVG also adapts to light/dark mode if styled with currentColor. |
| **KTD2** | Repo sections will use `<details>` + `<summary>` HTML. | Markdown tables inside `<details>` render correctly on GitHub and cut vertical noise without hiding information. |
| **KTD3** | Stats widgets will use `github-readme-stats` via `vercel.app` CDN. | The service is the de-facto standard, free, and updates dynamically. We accept the risk of occasional rate-limiting or downtime since these are decorative, not load-bearing. |
| **KTD4** | Header asset (if SVG) will live in `assets/header.svg` and be referenced with a repo-relative path. | Self-hosted, survives as long as the repo does, and works with GitHub's image cache. |
| **KTD5** | No animated GIFs or heavy media. | Keeps the README fast, accessible, and easy to maintain. Motion is not central to this identity. |

## Scope Boundaries

### In scope
- Rewriting the opening section with a terminal/ASCII header block.
- Adding a "now" section.
- Wrapping each repo category table in a `<details>` block.
- Adding `github-readme-stats` cards for top languages and general GitHub stats.
- Committing an optional `assets/header.svg` if an SVG banner is produced.
- Verifying rendering on GitHub after commit/push.

### Deferred to follow-up work
- Generating an animated terminal GIF (retro terminal aesthetic) if we later want more motion.
- Adding a contribution snake or 3D contribution graph.
- Adding a "buy me a coffee" or sponsor section.
- Re-adding private repos once they are actually made public.

### Outside this product's identity
- Corporate boilerplate, skill badge sticker books, or long autobiographical text.
- External portfolio embeds or heavy JavaScript widgets.

## Implementation Units

### U1. Design and commit the header asset

**Goal:** Produce a self-hosted visual header that sets the tone.

**Requirements:** R2, R6

**Dependencies:** None

**Files:**
- `assets/header.svg` (new)
- `README.md` (modified)

**Approach:**
- Create an SVG banner with a dark terminal aesthetic: monospace font, prompt characters (`$`), and a short tagline.
- Use `currentColor` or separate light/dark fills so it remains readable in both GitHub themes.
- Reference it from `README.md` with a repo-relative Markdown image tag.

**Patterns to follow:**
- Keep the SVG hand-authored or generated from a simple script; no external dependencies.
- Match the README voice in any header copy.

**Test scenarios:**
- SVG opens and renders in a browser without errors.
- SVG text remains legible when the browser/os is toggled between light and dark modes.
- README image reference resolves correctly on the `main` branch after push.

**Verification:**
- `assets/header.svg` is present and under ~50 KB.
- The README preview shows the header on both light and dark themes.

### U2. Rewrite the README opening with terminal/ASCII identity

**Goal:** Replace the plain opening with something that feels like a developer typed it.

**Requirements:** R1, R2, R4

**Dependencies:** U1

**Files:**
- `README.md` (modified)

**Approach:**
- Lead with the SVG header or a pure ASCII block.
- Follow with a short "now" section: 2-3 bullets about what you are currently building or thinking about.
- Keep the existing voice: direct, slightly cynical, no filler.

**Patterns to follow:**
- The existing README already has the right tone; preserve it.

**Test scenarios:**
- Opening section contains no corporate phrases like "passionate about", "leveraging", or "synergy".
- "now" section lists concrete current work, not generic interests.

**Verification:**
- A human read of the first 20 lines feels like a person, not a product page.

### U3. Collapse repo catalog sections with `<details>`

**Goal:** Reduce vertical noise while keeping every repo one click away.

**Requirements:** R3, R7

**Dependencies:** None

**Files:**
- `README.md` (modified)

**Approach:**
- Wrap each existing `###` category section (Agents, Local-first, Tooling, etc.) in a `<details>` block.
- Use `<summary>` to name the section and show the repo count or a one-line teaser.
- Keep the inner markdown tables intact; GitHub renders tables inside `<details>`.

**Patterns to follow:**
- Existing GitHub profile READMEs use this pattern successfully.
- Do not nest `<details>` blocks.

**Test scenarios:**
- All six repo categories are collapsed by default.
- Expanding a `<details>` block reveals the full table with working links.
- Markdown links and table formatting remain valid.

**Verification:**
- README renders without broken HTML on GitHub.
- Each collapsed section shows a meaningful `<summary>` line.

### U4. Add dynamic GitHub stats widgets

**Goal:** Give visitors a quick quantitative snapshot without manually updating numbers.

**Requirements:** R5

**Dependencies:** None

**Files:**
- `README.md` (modified)

**Approach:**
- Add two images near the bottom:
  - General GitHub stats card: `https://github-readme-stats.vercel.app/api?username=fraction12&theme=dark&show_icons=true`
  - Top languages card: `https://github-readme-stats.vercel.app/api/top-langs?username=fraction12&layout=compact&theme=dark`
- Include `hide_border` or `count_private` flags only if they improve the look.
- Place them after the repo catalog so they do not distract from the work itself.

**Patterns to follow:**
- Use a `theme` parameter that works in dark mode; optionally add a light-mode fallback comment for future improvement.

**Test scenarios:**
- Image URLs return 200 and render a card when visited directly.
- Cards appear in the README after push.
- If the service is rate-limited, a broken-image alt text still describes the section.

**Verification:**
- README preview shows both cards.
- Cards update automatically as repos and languages change.

### U5. Commit, push, and verify on GitHub

**Goal:** Ship the updated profile and confirm it looks right live.

**Requirements:** R8

**Dependencies:** U1, U2, U3, U4

**Files:**
- `README.md` (modified)
- `assets/header.svg` (new)

**Approach:**
- Stage changes, commit with a clear message, and push to `origin/main`.
- Open `https://github.com/fraction12` in a browser to verify the header, collapsed sections, and stats cards render correctly.
- Screenshot or describe any rendering issues for follow-up.

**Patterns to follow:**
- Use a single commit for the README upgrade unless issues are found during verification.

**Test scenarios:**
- The profile page loads without broken images.
- All `<details>` sections expand and collapse.
- Stats cards display expected data.

**Verification:**
- Visual confirmation on GitHub in both light and dark themes.

## Open Questions

- **Q1.** Should the dynamic stats cards use a `theme` that adapts to light/dark mode, or is a single dark theme acceptable?
  - *Recommendation:* Use `theme=dark` for now. GitHub does not support `prefers-color-scheme` in README images cleanly; a future follow-up can add picture-element fallbacks.
- **Q2.** If `github-readme-stats` is down, do we want a fallback or just accept broken images?
  - *Recommendation:* Accept broken images as a graceful degradation; these are decorative. Add alt text that still communicates the section meaning.

## Risks & Dependencies

| Risk | Mitigation |
| --- | --- |
| `github-readme-stats` rate-limits or goes down | Keep stats as optional decoration, not the main content. Add descriptive alt text. |
| SVG header does not look good in both themes | Test with `currentColor` or provide two SVGs/light-dark fallbacks. |
| `<details>` blocks break table rendering | Validate in GitHub preview before considering done. |
| README becomes too long or gimmicky | Stop at header + now + collapsed sections + stats. No more widgets. |

## Sources & Research

- `fraction12/fraction12` README, current `main` — baseline content and voice.
- `github.com/coderjojo/creative-profile-readme` — curated examples of creative profile READMEs.
- `github.com/anuraghazra/github-readme-stats` — dynamic stats cards.
- `github.com/kyechan99/capsule-render` — header banner generation options (rejected in favor of a self-hosted SVG).
- `github.com/topics/beautiful-profile-readme` — community examples and trends.

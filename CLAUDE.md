# CLAUDE.md

Guidance for AI assistants working in this repository.

## What this repo is

A **single-page, self-contained HTML presentation board** that documents the HOSPO DOJO
performance marketing strategy for Q1 2026. It is a client-facing deliverable — a visual
strategy document — not an application.

```
hospo-dojo-perfomance-strategy/
└── index.html    # the entire deliverable (~1,400 lines: CSS in <head>, content in <body>)
```

That is the whole repository. There is no `package.json`, no build step, no test suite,
no CI configuration, no dependency manifest, and no `.github/` directory. Do not add
tooling (bundlers, frameworks, linters, npm scripts) unless explicitly asked — the value
of this file is that it opens in any browser with zero setup.

Note the repo name contains a typo (`perfomance`). Leave it alone; renaming would break
the remote and any published URL.

## Architecture of `index.html`

Three parts, in order:

1. **`<head>`** — meta tags, title, and a single external dependency: Google Fonts
   (`Space Grotesk` 400/600/700, `Inter` 400/500/700/900). Fonts are the only network
   request the page makes.
2. **`<style>`** (lines ~9–652) — all CSS. Organized in commented blocks: `:root` tokens,
   base/body, header, grid, cards, metric cards, section headers, code blocks, lists,
   ICP cards, budget table, technical setup, landing pages, dashboard, responsive,
   animations, scaling note.
3. **`<body>`** — one `.board-container` wrapping every content section.

There is **no JavaScript anywhere** (zero `<script>` tags) and **no images** (zero `<img>`
tags). All iconography is inline emoji. Keep it that way: the page must stay renderable
from a `file://` URL with no assets alongside it.

### Content sections in `<body>`

| Order | Section | Markup pattern |
|---|---|---|
| 1 | Header (title, author, KPI badges) | `header.board-header` + `.board-meta` |
| 2 | 📊 Strategy Overview | `.board-grid` with 4 × `.card` |
| 3 | 💰 Budget Allocation | `.budget-table` (header row, 4 channel rows, total row) + `.scaling-note` |
| 4 | 🎯 Ideal Customer Profiles | 3 × `.icp-card`, each with 5 × `.icp-section` |
| 5 | 🔧 Technical Setup Roadmap | warning callout + 3 × `.setup-card` with numbered `.setup-step`s |
| 6 | 🎨 Landing Page Strategy | 4 × `.landing-card` + A/B testing roadmap block |
| 7 | 📊 Measurement Dashboard | `.dashboard-section` with 3 × `.metric-group` of `.metric-item`s |
| 8 | Footer CTA + attribution | inline-styled gradient block, then `<p>` attribution |

Every top-level section is introduced by a `.section-header` containing an
`h2.section-title` (emoji + name) and a `p.section-subtitle`. Follow that pattern when
adding a section, and match the existing HTML comment style (`<!-- ICP Section -->`).

## Design system

All colors, gradients, and surfaces come from CSS custom properties on `:root`. **Use the
tokens; never hard-code a hex value.**

```
--bg-dark        #0a0e14   page background
--bg-card        #16191f   card surface
--bg-hover       #1e2329   (defined, currently unused)
--accent-primary #00ff88   green — the brand accent; targets, "good", highlights
--accent-danger  #ff3366   red — critical badges, highest price point
--accent-warning #ffaa00   amber — priority callouts, warnings
--accent-info    #00aaff   blue — informational, secondary price point
--accent-purple  #8b5cf6   (defined, only used inside .icp-card's gradient)
--text-primary   #ffffff
--text-secondary #8b92a1   body copy inside cards
--border         #2a2f3a
--gradient-1..4            135deg linear gradients; --gradient-4 (green/teal) is the
                           brand gradient used by the title, section markers, footer CTA
```

Typography convention: `Inter` for body copy, `Space Grotesk` for anything numeric or
monospace-flavored (metric values, badges, `.board-meta`, code blocks, budget figures).

Dark theme only — there is no light-mode variant and no `prefers-color-scheme` handling.
Don't introduce one unless asked.

### Styling conventions (important)

- **Reusable patterns live in the `<style>` block as classes.** One-off tweaks are done
  with inline `style="…"` attributes, and the file has ~117 of them. This is the
  established convention, not an accident. When editing, follow the local pattern: if a
  card needs an unusual color or font size for one instance, inline it; if you're adding a
  third instance of something, promote it to a class in the appropriate commented CSS block.
- **Semantic-ish class names, no utility framework.** No Tailwind, no BEM. Classes are
  named after the thing they are (`.icp-card`, `.budget-table-row`, `.setup-step-number`).
- **Cards animate in** via the `slideIn` keyframe with staggered `animation-delay` on
  `.card:nth-child(1..6)`. Only the first 6 children get a delay — a 7th card in a grid
  will animate with no delay. Extend the nth-child list if you add more.
- **Responsive breakpoint is a single `@media (max-width: 768px)`** that collapses grids to
  one column and shrinks the display type. Any new multi-column layout needs a rule there
  too. `.board-container` is capped at `max-width: 2400px` — this is designed for very wide
  screens.
- **Some CSS is defined but unused in the markup**: `.photo-placeholder`, `.metric-card` /
  `.metric-value` / `.metric-label`, `.code-block`, `.card-badge` and its
  `.badge-critical` / `.badge-priority` / `.badge-info` / `.badge-success` variants, and
  `--gradient-3`. These are available scaffolding for future sections. Don't delete them as
  "dead code" without asking, and prefer reusing them over inventing new equivalents.
- **Raw `<` appears in text content** (e.g. `< AUD 60` for "under AUD 60"). HTML5 parsers
  treat `<` followed by a space as literal text, so it renders correctly. If you touch
  those lines, `&lt;` is the safer form, but don't churn the file just to escape them.

## Content conventions

This is a marketing strategy document, so content accuracy matters as much as markup.

- **Currency is always Australian dollars, written `AUD 1,000`** — currency code prefix,
  space, comma thousands separator. Never `$1000` or `A$1,000`.
- **First-person voice.** The document is written as the strategist speaking ("My goal
  is…", "I'm starting with…", "What I'm tracking weekly"). Preserve that voice; don't
  rewrite to third person or passive.
- **Emoji prefix every section title and most card titles.** Match the existing tone.
- **Numbers must stay internally consistent.** The KPI targets and budget figures are
  repeated in several places, so a change in one spot usually needs changes in others:
  - Header badges: MAX CAC `AUD 1,000`, Target CPL `< AUD 60`.
  - Core targets: CPL `< AUD 60`, Lead→SQL `20%+`, Cost per SQL `< AUD 300`,
    SQL→Close `30%`. These appear in Strategy Overview *and* the Measurement Dashboard.
  - Budget table monthly totals — `AUD 4,250` / `4,500` / `5,500` — must equal the sum of
    the Meta / Google / LinkedIn / YouTube rows for that month.
  - The dashboard's weekly spend target (`AUD 1,063/week`) is derived from Month 1's
    `AUD 4,250`. Recompute it if the budget changes.
  - Scaling thresholds in `.scaling-note` (`< AUD 250` scale, `250–350` hold, `> AUD 400`
    cut) are keyed to the `AUD 300` cost-per-SQL target.
- **Three ICPs, one per product**, each with the same five subsections in the same order:
  Demographics, Business Characteristics, Pain Points, Goals & Motivations, Where They
  Hang Out. Personas are Sarah (Mentorship, AUD 3–5k), Mark (Consulting, AUD 8.5k), Alex
  (Kitchen Design, AUD 25k+). A fourth product is referenced in the copy ("4-product
  portfolio", webinars as the all-products funnel) but has no ICP card.
- **Known inconsistency to flag, not silently fix:** the header credits
  "Daniel Bogdantsev" while the footer credits "Danylo Bohdantsev". These are two
  transliterations of the same name. If you're editing either line, ask which spelling the
  document should use rather than picking one.

## Workflow

### Previewing

No build, no server needed. Open the file directly:

```bash
xdg-open index.html          # or: open index.html on macOS
python3 -m http.server 8000  # if you need an http:// origin
```

Verification is visual. There are no tests to run and nothing to compile. After a change,
confirm the page still renders — check the section you touched, then check the 768px
mobile layout, since the single breakpoint is easy to break.

Because the whole page is one file, prefer `Edit` with a unique surrounding anchor over
rewriting `index.html` wholesale — a full `Write` risks losing unrelated content.

### Git

- The default branch is `main`.
- **Develop on the branch you were assigned** (currently
  `claude/claude-md-documentation-p0n7ig`). Never push to `main`.
- Push with `git push -u origin <branch-name>`, then open a **draft** pull request if one
  isn't already open for that branch. There is no PR template in this repo.
- Existing commit messages are terse GitHub-web-UI defaults ("Update index.html"). Write
  proper descriptive messages instead; don't imitate that style.
- History note: the file was uploaded via the GitHub web UI and renamed from
  `hospo_dojo_miro_board (1).html` to `index.html` — the `index.html` name is what makes
  the board servable as a static site root, so keep it.

## Boundaries

- Don't add a framework, build pipeline, or dependency manifest.
- Don't split `index.html` into separate CSS/JS files — self-containment is the point.
- Don't add JavaScript unless a request genuinely requires interactivity.
- Don't change strategy numbers, budgets, or KPI targets on your own initiative. They are
  business decisions made by the author. Fix markup and presentation freely; surface
  content questions to the user.

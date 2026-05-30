---
name: saas-redesign
description: Redesign a Vue 3 app's UI into a modern SaaS interface — left sidebar nav, a CSS-variable theme (indigo primary, bright-green accent, light blue-grey background), and consistent spacing. Use this skill when the user wants to modernize/redesign a Vue 3 frontend, convert a top nav bar to a vertical sidebar, or apply a consistent design system.
---

# SaaS UI Redesign for Vue 3

A repeatable procedure for turning a Vue 3 single-page app into a modern, professional SaaS-style
interface: a **left vertical sidebar** (replacing a top nav bar), a **CSS-variable design system**
(indigo primary, bright-green accent, light blue-grey background), and **consistent spacing**.

This skill is **generic** — it discovers the app's shell, router, and colors at runtime — but every
step includes an **"In this repo →"** callout grounding it to this inventory-management app, which is
its reference target. Follow the steps in order; theming (Step 1) must come before recoloring
(Step 4).

## Hard Rules (non-negotiable)

1. **Never edit `.vue` files directly. Delegate every `.vue` create/modify to the `vue-expert`
   subagent via the Task tool.** This is mandated by the root `CLAUDE.md`. Hand `vue-expert` the
   token block, the sidebar spec, and the relevant slice of the color-mapping table for each file.
2. **No emojis anywhere in the UI** — this is a business application.
3. **Verify visually with Playwright MCP** (`mcp__playwright__*`) against `http://localhost:3000`
   before declaring done. Capture before/after screenshots.
4. **Preserve all existing behavior** — routes, labels, events, modal wiring, i18n keys, data
   loading. This is a visual redesign, not a refactor of functionality.
5. **Checkpoint first** (Step 0). Never start a Full redesign on an uncommitted working tree.

## Target Design System

Ship this exact token set. Introduce it as a `:root` block in the global stylesheet **before**
touching any colors.

```css
:root {
  /* Brand */
  --color-primary:#4f46e5; --color-primary-hover:#4338ca; --color-primary-soft:#eef2ff;
  --color-accent:#22c55e;  --color-accent-hover:#16a34a;  --color-accent-soft:#dcfce7;
  /* Surfaces */
  --bg:#eef2f7; --surface:#ffffff; --surface-2:#f1f5f9;
  /* Text */
  --text:#0f172a; --text-muted:#64748b; --text-subtle:#94a3b8;
  /* Lines */
  --border:#e2e8f0; --border-strong:#cbd5e1;
  /* Status */
  --success:#22c55e; --success-soft:#dcfce7; --success-ink:#166534;
  --warning:#d97706; --warning-soft:#fef3c7; --warning-ink:#92400e;
  --danger:#dc2626;  --danger-soft:#fee2e2;  --danger-ink:#991b1b;
  --info:#4f46e5;    --info-soft:#eef2ff;    --info-ink:#3730a3;
  /* Spacing scale (4px base) */
  --space-1:.25rem; --space-2:.5rem; --space-3:.75rem; --space-4:1rem;
  --space-5:1.5rem; --space-6:2rem; --space-8:3rem;
  /* Radius / shadow / layout */
  --radius:10px; --radius-sm:6px; --radius-lg:14px;
  --shadow-sm:0 1px 2px rgba(15,23,42,.06);
  --shadow:0 1px 3px rgba(15,23,42,.08),0 1px 2px rgba(15,23,42,.04);
  --sidebar-w:248px; --content-max:1280px;
}
```

**Design intent:** indigo for primary/interactive states (active nav, links, primary buttons,
avatars), bright green for positive/success states and accenting, white surfaces on a light
blue-grey page background, generous whitespace via the spacing scale, soft rounded corners, and
subtle shadows. Clean, calm, professional.

## Step 0 — Checkpoint & locate the shell

1. Ensure a clean git tree, or create a checkpoint branch first.
   - **In this repo →** run the `/demo-branch` command to start a fresh demo branch.
2. Locate the **app shell**: the file containing the nav markup, `<router-view>`, and the global
   styles. Locate the **router** config and note which components are global (rendered in the shell)
   vs. view-local.
   - **In this repo →** the shell *and* the de-facto global stylesheet are both
     `client/src/App.vue` (its `<style>` is unscoped and defines `.card`, `.stats-grid`,
     `.badge.*`, `.page-header`, table styles, etc.). Routes live in `client/src/main.js`. Global
     components: `FilterBar`, `LanguageSwitcher`, `ProfileMenu`, `ProfileDetailsModal`, `TasksModal`.

## Step 1 — Establish the design-token layer (do this first)

Add the `:root` token block above as the single source of truth, then build everything against
`var(--token)` references. Do **not** recolor components yet — just introduce the tokens.

- **In this repo →** prepend the `:root` block to the top of App.vue's unscoped `<style>` block.
  (Alternative: create `client/src/styles/theme.css` and import it in `client/src/main.js` — only do
  this if the user prefers a separate theme file; the default is inline in App.vue, where the repo
  already points its "single source of truth.")

## Step 2 — Convert the top nav to a left sidebar

1. Change the shell layout from a vertical flex column to a two-column grid:
   ```css
   .app { display:grid; grid-template-columns:var(--sidebar-w) 1fr; min-height:100vh; }
   ```
2. Build a vertical `<aside class="sidebar">`:
   - **Top:** logo / brand block.
   - **Middle:** the nav links, stacked vertically (`display:flex; flex-direction:column; gap:var(--space-1)`).
   - **Bottom:** `LanguageSwitcher` and `ProfileMenu`, pinned with `margin-top:auto`.
   - Sidebar styling: `background:var(--surface); border-right:1px solid var(--border);
     position:sticky; top:0; height:100vh; padding:var(--space-5) var(--space-4)`.
3. **Active nav state:** replace any top-nav underline indicator with a filled indigo "soft pill"
   (`background:var(--color-primary-soft); color:var(--color-primary); border-radius:var(--radius-sm)`),
   optionally with a left accent bar. Keep the same active-route logic.
4. **Content column:** the right column holds the (re-anchored) filter bar and the main content.
   `.main-content` stays centered with `max-width:var(--content-max); margin:0 auto;
   padding:var(--space-5) var(--space-6)`.
5. **Re-anchor any sticky element that assumed the old nav height.** A top nav is typically followed
   by elements offset by its height; in a sidebar layout those become `top:0`.
   - **In this repo →** `client/src/components/FilterBar.vue` is hardcoded `position:sticky;
     top:70px` (it assumed the 70px top nav). Change it to `top:0`. Also remove the
     `.nav-container > .nav-tabs { margin-left:auto }` right-align hack from App.vue — it only makes
     sense horizontally.

**Preserve behavior:** same routes and labels, same `ProfileMenu` emits (`show-profile-details`,
`show-tasks`), same modal wiring, same i18n keys. (In this repo, the `Reports` link label is a
hardcoded literal today — keep it as-is.)

## Step 3 — Consistent spacing

- Normalize all gutters/padding to the spacing-scale tokens (`--space-*`) instead of ad-hoc `rem`
  values.
- Standardize the card system on tokens:
  ```css
  .card { background:var(--surface); border:1px solid var(--border);
          border-radius:var(--radius); box-shadow:var(--shadow-sm); padding:var(--space-5); }
  ```
- Reconcile competing max-width containers onto the new layout.
  - **In this repo →** three places hardcode `max-width:1600px` (`.nav-container`, `.main-content`,
    `.filters-container`). The sidebar replaces `.nav-container`; align `.main-content` and the
    filter bar to `var(--content-max)` within the content column.

## Step 4 — Full color sweep

Replace **every** hex literal with a token across all `.vue` files. Use this mapping:

| Old (typical) | Role | New token / value |
|---|---|---|
| `#2563eb` (blue-600) | primary accent (active nav, badges, avatar) | `--color-primary` `#4f46e5` |
| `#3b82f6` (blue-500) | focus ring, chart bars, legend dots | `--color-primary` (or a lighter `#6366f1` for a 2nd tint) |
| `#eff6ff` (blue-50) | active-nav / selected background | `--color-primary-soft` `#eef2ff` |
| `#10b981` / `#059669` | success green | `--color-accent` `#22c55e` / `--color-accent-hover` `#16a34a` |
| `#f8fafc` | page background | `--bg` `#eef2f7` |
| `#667eea` → `#764ba2` | purple hero/summary gradient | indigo gradient `linear-gradient(135deg,#4f46e5,#4338ca)` |
| `#0f172a` / `#64748b` / `#e2e8f0` | text / muted / border | `--text` / `--text-muted` / `--border` (values unchanged) |
| status fills (`#fecaca`, `#fed7aa`, `#d1fae5`, `#dbeafe`, …) | badge/stat backgrounds | matching `--*-soft` / `--*-ink` status tokens |

Tactics:
- **Work highest-density files first** so the bulk of the change lands early.
  - **In this repo →** `Dashboard.vue` (~59 literals), `App.vue` (~57), `TasksModal.vue` (~43),
    `InventoryDetailModal.vue` (~42), `Spending.vue` (~41).
- **Watch for views that re-declare global classes locally** — they won't inherit the retheme.
  - **In this repo →** `Reports.vue` locally re-declares `.card`/`.stat-card`/`.badge`; recolor it
    explicitly.
- Find remaining literals with: `grep -rn "#[0-9a-fA-F]\{6\}" client/src` and keep going until only
  intentional one-offs remain.

## Step 5 — Delegate & verify

1. **Delegate** each `.vue` file's changes to `vue-expert` (Task tool). Give it: the token block, the
   sidebar spec (Step 2), and the rows of the mapping table relevant to that file. Never edit `.vue`
   files yourself.
2. **Start the app.**
   - **In this repo →** run `/start` (backend on :8001, frontend on :3000).
3. **Visual verification with Playwright MCP** at `http://localhost:3000`. Navigate every route,
   screenshot each, and confirm:
   - Sidebar is on the left; top nav bar is gone.
   - Active nav + primary buttons/links are indigo; success values/accents are bright green.
   - Page background is light blue-grey; cards are white with consistent padding.
   - No leftover blue accent (`#2563eb` / `#3b82f6`); no emojis; no console errors.
   - **In this repo →** routes to check: `/`, `/inventory`, `/orders`, `/spending`, `/demand`,
     `/reports`.
4. Capture **before/after** screenshots for the user.

## Step 6 — Update design-system docs

Record the new palette so future work stays consistent.
- **In this repo →** update the "Design System" section of the root `CLAUDE.md` (and the styling
  notes in `client/CLAUDE.md`) to describe the token-based indigo/green/light-blue-grey system and
  point to the `:root` block as the source of truth.

## Verifying the redesign succeeded

```bash
# Old accent/background literals should trend toward zero after a Full sweep:
grep -rn "#2563eb\|#3b82f6\|#f8fafc" client/src

# The old sticky offset should be gone:
grep -rn "top: 70px" client/src/components/FilterBar.vue
```

All six routes render without console errors, the sidebar is present on every route, and the theme
reads indigo + green on light blue-grey.

## Key Reminders

- **Tokens before colors** — introduce the `:root` variables (Step 1) before any recolor (Step 4).
- **All `.vue` edits go through `vue-expert`** — no direct edits, ever.
- **No emojis. No behavior changes.** Visual redesign only.
- **Re-anchor sticky elements** that depended on the old top-nav height (e.g. `FilterBar` `top:70px`
  → `top:0`).
- **Recolor views that re-declare global classes locally** (e.g. `Reports.vue`).
- **Verify with Playwright at `localhost:3000`** across every route, with before/after screenshots,
  before calling it done.
- **Checkpoint first** with `/demo-branch`; never run a Full redesign on a dirty tree.

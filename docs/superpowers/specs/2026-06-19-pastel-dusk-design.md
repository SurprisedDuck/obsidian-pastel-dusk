# Pastel Dusk — Design Spec

**Date:** 2026-06-19
**Repo:** [SurprisedDuck/obsidian-pastel-dusk](https://github.com/SurprisedDuck/obsidian-pastel-dusk) (public)
**Status:** built, installed, and iterated in-app

> **Update (2026-06-22) — where we landed.** The palette evolved well past this original
> spec through live iteration. Final identity: a **pastel turquoise** accent (`#9fd6cb`) for
> buttons / tags / active states; **pastel blue** links (`#97b8e0`) and file names (`#c8e0f6`);
> **six distinct pastel heading colours** (turquoise · blue · green · lilac · rose · amber);
> a deep near-neutral background (`#1d1f20`, with a darker left sidebar `#0f1112`); soft
> rounded corners; and floating panes. **`theme.css` is the source of truth for exact
> colours** — the values in the sections below reflect the earlier "frost" design and are
> kept for history.

## Overview

Pastel Dusk is a **dark-only** Obsidian theme — a calmer, deeper, pastel take on the Nord
palette. It is a derivative of [insanum/obsidian_nord](https://github.com/insanum/obsidian_nord)
("Obsidian Nord" by Eric Davis, MIT). We keep that theme's clean variable architecture and
re-map it to our own identity.

### Base & licensing

- Built on insanum/obsidian_nord `theme.css` (MIT, © 2020 Eric Davis).
- Our `LICENSE` preserves Eric Davis's copyright and adds SurprisedDuck's (already committed).
- Nord palette © Arctic Ice Studio / nordtheme.com — credited in README + theme header.

## Scope

**In scope (what makes it "ours"):**
- Dark mode only.
- Deeper background (`#242933`) with a deepest backdrop for floating panes.
- Unified **pastel Frost teal** accent across links, buttons, active states, inline title, tags.
- **Frost-gradient headings** (cool, monochromatic — not Nord's rainbow).
- Pastel-shifted accent palette (Frost + Aurora).
- **Rounded corners**: panes float as rounded cards inside a rounded window; rounded tabs, code,
  tables, callouts, inputs, buttons, embeds.

**Kept from stock Nord (inherited, lightly recolored):**
- Semantic callout colors, table striping, checkboxes (recolored to teal), graph view, syntax colors.

**Non-goals (YAGNI for v1):**
- Light mode (explicitly dropped).
- Custom fonts / typography changes (keep Obsidian defaults).
- Style Settings plugin toggles, per-community-plugin styling.
- Obsidian community gallery submission (possible later; repo is public + MIT-ready).

## Architecture

Single `theme.css` + `manifest.json`, following the base theme's structure:

1. **`:root`** — palette as reusable variables (RGB triplets + hex), extended with Pastel Dusk's
   deep backgrounds and pastel Frost/Aurora shades.
2. **`body`** — global tokens: HSL accent, link/tag decorations, radii, weights.
3. **`.theme-dark`** — maps the palette onto Obsidian's variables (the bulk of the theme).
4. **Structural section** — the small amount of real CSS needed for rounded floating panes
   (Obsidian has no variable for this), plus radius application to embeds/images.
5. The stock `.theme-light` block is **removed**.

Header comment credits insanum + Nord + MIT.

## Color system

### Backgrounds (dark only)
| Token | Hex | Use |
|---|---|---|
| backdrop | `#15181e` | behind floating panes / window backdrop |
| sidebar | `#1b1f27` | `--background-secondary`, code block bg, table header |
| editor | `#242933` | `--background-primary` |
| surface | `#2e3440` | borders, tag bg, table cell borders |
| surface-hi | `#3b4252` | hover/active surfaces |

### Pastel Dusk (accent family)
| Token | Hex | Use |
|---|---|---|
| teal | `#a8cbca` | **accent**, H1, internal+external links, inline title, tags, checkboxes, button bg |
| cyan | `#a2ceda` | H2, inline + block code (`--code-normal`), highlight background |
| blue | `#9db6cf` | H3, info callout |
| indigo | `#819dbe` | H4 |

### Text
| Token | Hex | Use |
|---|---|---|
| normal | `#d8dee9` | body (`--text-normal`) |
| emphasis | `#eceff4` | bold (`--bold-color`), brightest |
| muted | `#8b93a3` | `--text-muted`, H5 |
| faint | `#6c7484` | `--text-faint`, H6, section labels |

### Pastel Aurora (semantic / syntax / callouts → `--color-*`)
red `#cf9aa0` · orange `#d6a98f` · yellow `#e6d2a8` · green `#b5ca9f` · purple `#c3a9bd`
(plus cyan `#a2ceda`, blue `#9db6cf` for `--color-cyan` / `--color-blue`).

### Key Obsidian variable mappings (`.theme-dark`)
- `--background-primary: #242933`; `--background-primary-alt: #242933`
- `--background-secondary: #1b1f27`; `--background-secondary-alt: #20252e`
- `--background-modifier-border: #2e3440`
- Accent (HSL → drives `--interactive-accent`, selection, etc.): `--accent-h: 178; --accent-s: 25%; --accent-l: 73%`
- `--text-on-accent: #242933` (dark text on the pastel button)
- `--text-accent: #a8cbca`; `--text-accent-hover: #a2ceda`
- `--link-url: #a8cbca`; external links get `--link-external-decoration: underline dotted`
- `--h1..h6-color: #a8cbca, #a2ceda, #9db6cf, #819dbe, #8b93a3, #6c7484`
- `--inline-title-color: #a8cbca`
- `--bold-color: #eceff4`; `--bold-weight: 600`; italic left uncolored
- `--code-normal: #a2ceda`; `--code-background: #1b1f27`
- `--text-highlight-bg: #a2ceda`; `--text-highlight-fg: #242933`
- `--tag-color: #a8cbca`; `--tag-background: #2e3440`; `--tag-background-hover: #3b4252`
- `--checkbox-color`/`--checkbox-border-color: #a8cbca`; `--checklist-done-color: #8b93a3`
- `--cursor-line-background: rgba(168,203,202,.08)`; `--text-selection: rgba(168,203,202,.30)`
- Nav: `--nav-item-color-hover: #a2ceda`; `--nav-item-color-active: #a8cbca` (active bg teal @ ~15%)
- `--titlebar-text-color-focused: #a8cbca`; focused/active tab text `#a8cbca`
- Graph: `--graph-line: #2e3440`; `--graph-node: #d8dee9`; `--graph-node-tag: #a8cbca`; `--graph-node-attachment: #b5ca9f`

## Rounding

- Radius scale: `--radius-s: 6px`, `--radius-m: 8px`, `--radius-l: 12px`.
- **Floating panes**: workspace background = backdrop `#15181e`; workspace leaves rendered as
  cards with `border-radius: 10px` and a small gap between them. Exact selectors
  (`.workspace-leaf` / `.workspace-tab-container` / tab headers) to be confirmed against the
  current Obsidian DOM during implementation, then verified visually in-app.
- Window corner radius `~14px` where the platform allows.
- Radius applied to: tabs, code blocks, tables (clipped corners), tags, callouts, inputs,
  buttons, images & embeds.

## File structure

```
obsidian-pastel-dusk/
  theme.css          # the theme (to build)
  manifest.json      # name "Pastel Dusk", author SurprisedDuck, version 0.1.0, minAppVersion 0.16.0
  README.md          # done
  LICENSE            # done (MIT, dual copyright)
  sample/            # a test note exercising every styled element (for verification)
  docs/superpowers/specs/2026-06-19-pastel-dusk-design.md  # this spec
```

## Verification

No Obsidian vault exists on this machine, and Obsidian can't be driven headlessly here, so
verification is **visual, in Obsidian**:

1. Author `sample/Pastel Dusk Showcase.md` covering H1–H6, body, bold/italic, internal/external
   links, highlight, inline + fenced code, every callout type, a table, task list, tags, and an embed.
2. Install the theme into a vault (`<vault>/.obsidian/themes/Pastel Dusk/`), enable it, open the
   showcase note in both reading and editing views.
3. Confirm against this spec; iterate from screenshots the user shares.

Optionally, a lightweight standalone `preview.html` that loads `theme.css` against a faithful
snippet of Obsidian's DOM classes can catch gross variable errors before in-app testing.

## Open questions / deferred

- Whether to add a `dark.png` screenshot + community-gallery `manifest.json` fields (deferred to a
  later "publish" pass).
- Style Settings support to expose accent/depth as toggles (future enhancement).

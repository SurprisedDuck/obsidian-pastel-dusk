# Pastel Dusk Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build "Pastel Dusk", a dark-only Obsidian theme — a soft pastel take on Nord — by assembling `theme.css` + `manifest.json` per the approved design spec.

**Architecture:** A single `theme.css` following the structure of the base theme (insanum/obsidian_nord, MIT): a `:root` palette of reusable variables, a `body` block of global tokens (HSL accent, decorations, radii), a `.theme-dark` block mapping the palette onto Obsidian's variables, and a small structural section for rounded floating panes. No light mode. We build the file section-by-section, verifying each against a local HTML preview harness, then confirm the whole thing in Obsidian against a showcase note.

**Tech Stack:** Plain CSS (Obsidian theme variables), JSON manifest. Local verification via a static `preview.html` served with Python's `http.server` and the preview MCP tools (`preview_start`, `preview_screenshot`, `preview_eval`).

**Spec:** `docs/superpowers/specs/2026-06-19-pastel-dusk-design.md`
**Repo:** `SurprisedDuck/obsidian-pastel-dusk` (origin already set; commit + push each task).

---

## File Structure

```
obsidian-pastel-dusk/
  manifest.json     # Task 1 — theme metadata, name "Pastel Dusk"
  theme.css         # Tasks 1,3–9 — the theme, built section by section
  preview.html      # Task 2 — local dev verification harness (gitignored, not part of the theme)
  sample/
    Pastel Dusk Showcase.md   # Task 10 — in-Obsidian verification note
  README.md         # Task 11 — install section finalized (already exists)
  LICENSE           # exists (MIT, dual copyright)
```

`theme.css` responsibility map (one file, ordered sections):
1. Credit header comment
2. `:root` — palette (deep backgrounds, pastel Frost, pastel Aurora, RGB triplets)
3. `body` — accent HSL, link/tag decorations, weights, radius scale
4. `.theme-dark` — backgrounds, text, headings, links, emphasis, code, highlight, tags, checkboxes, selection, semantic colors, tables, nav/chrome, graph
5. Structural CSS — rounded floating panes, highlight foreground, rounded media

---

## Task 1: Scaffold manifest + theme.css header

**Files:**
- Create: `manifest.json`
- Create: `theme.css`

- [ ] **Step 1: Capture the base theme for reference**

Run:
```bash
cd "/Users/jannik/Documents/Claude Code/obsidian-theme"
curl -sL https://raw.githubusercontent.com/insanum/obsidian_nord/master/theme.css -o /tmp/nord-base.css
wc -l /tmp/nord-base.css
```
Expected: prints `525 /tmp/nord-base.css`. This is reference only — we author our own file; do not copy the light-mode block.

- [ ] **Step 2: Create `manifest.json`**

```json
{
   "name": "Pastel Dusk",
   "version": "0.1.0",
   "minAppVersion": "0.16.0",
   "author": "SurprisedDuck",
   "authorUrl": "https://github.com/SurprisedDuck"
}
```

- [ ] **Step 3: Create `theme.css` with the credit header only**

```css
/*
 * Pastel Dusk — a dark-only Obsidian theme.
 * A soft, pastel take on the Nord palette.
 *
 * Based on "Obsidian Nord" by Eric Davis (insanum)
 *   https://github.com/insanum/obsidian_nord  (MIT, (c) 2020 Eric Davis)
 * Nord palette (c) Arctic Ice Studio — https://www.nordtheme.com
 * Pastel Dusk modifications (c) 2026 SurprisedDuck. MIT.
 */
```

- [ ] **Step 4: Commit**

```bash
git add manifest.json theme.css
git -c core.pager=cat commit -m "feat: scaffold manifest and theme.css header" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 2: Local preview harness (verification rig)

**Files:**
- Create: `preview.html`
- Modify: `.gitignore`

- [ ] **Step 1: Create `preview.html`**

This page loads `theme.css`, sets `class="theme-dark"`, and renders representative markup that consumes our variables directly. It is our visual test surface.

```html
<!DOCTYPE html>
<html class="theme-dark">
<head>
  <meta charset="utf-8">
  <link rel="stylesheet" href="theme.css">
  <style>
    body { margin: 0; font-family: -apple-system, system-ui, sans-serif;
           background: var(--pf-backdrop, #000); }
    .app { display: flex; gap: 6px; padding: 6px;
           background: var(--pf-backdrop); min-height: 100vh; }
    .side { width: 230px; padding: 14px; border-radius: var(--radius-l);
            background: var(--background-secondary); color: var(--text-muted); }
    .side .active { color: var(--nav-item-color-active);
                    background: var(--nav-item-background-active);
                    border-radius: var(--radius-m); padding: 3px 6px; }
    .view { flex: 1; padding: 24px 32px; border-radius: var(--radius-l);
            background: var(--background-primary); color: var(--text-normal);
            line-height: 1.6; }
    h1 { color: var(--h1-color); } h2 { color: var(--h2-color); }
    h3 { color: var(--h3-color); } h4 { color: var(--h4-color); }
    h5 { color: var(--h5-color); } h6 { color: var(--h6-color); }
    .title { color: var(--inline-title-color); font-size: 2em; font-weight: 800; }
    a { color: var(--text-accent); text-decoration: var(--link-decoration); }
    a.ext { color: var(--link-external-color); text-decoration: var(--link-external-decoration); }
    strong { color: var(--bold-color); font-weight: var(--bold-weight); }
    mark { background: var(--text-highlight-bg); color: var(--text-highlight-fg); padding: 0 4px; border-radius: 4px; }
    code { background: var(--code-background); color: var(--code-normal);
           padding: 1px 6px; border-radius: var(--radius-s); font-family: monospace; }
    pre { background: var(--code-background); color: var(--code-normal);
          padding: 12px 14px; border-radius: var(--radius-m); }
    .tag { background: var(--tag-background); color: var(--tag-color);
           padding: 2px 9px; border-radius: 9px; font-size: .85em; }
    .callout { background: rgba(157,182,207,.13); border-left: 3px solid var(--color-blue);
               border-radius: var(--radius-m); padding: 10px 14px; margin: 12px 0; }
    .callout .ctitle { color: var(--color-blue); font-weight: 700; }
    table { border-collapse: separate; border-spacing: 0; width: 100%;
            border: 1px solid var(--background-modifier-border);
            border-radius: var(--radius-m); overflow: hidden; margin: 12px 0; }
    th { background: var(--table-header-background); color: var(--tag-color);
         text-align: left; padding: 6px 10px; }
    tr:nth-child(even) td { background: var(--table-row-even-background); }
    tr:nth-child(odd) td  { background: var(--table-row-odd-background); }
    td { padding: 6px 10px; }
    .done { color: var(--checklist-done-color); text-decoration: line-through; }
    .btn { background: hsl(var(--accent-h), var(--accent-s), var(--accent-l));
           color: var(--text-on-accent); border: 0; padding: 7px 16px;
           border-radius: var(--radius-m); font-weight: 600; }
  </style>
</head>
<body>
  <div class="app">
    <div class="side">
      <div style="font-size:.7em;letter-spacing:.08em;color:var(--text-faint)">VAULT</div>
      <div>Daily note</div>
      <div class="active">Project notes</div>
      <div>Ideas</div>
      <div style="margin-top:14px"><span class="tag">#nord</span> <span class="tag">#theme</span></div>
    </div>
    <div class="view">
      <div class="title">Project notes</div>
      <h1>Heading one</h1>
      <p>Body with an <a href="#">internal link</a>, an <a class="ext" href="#">external link</a>,
         <strong>bold</strong>, <em>italic</em>, and a <mark>highlight</mark>.</p>
      <h2>Heading two</h2>
      <p>Inline <code>code()</code> in soft cyan.</p>
      <div class="callout"><div class="ctitle">&#8505; Note</div>Callouts keep Nord semantic colours.</div>
      <h3>Heading three</h3>
      <pre>const greet = (name) =&gt; `hi ${name}`;</pre>
      <h4>Heading four</h4>
      <table><tr><th>Decision</th><th>Value</th></tr>
        <tr><td>Accent</td><td>#a8cbca</td></tr>
        <tr><td>Background</td><td>#242933</td></tr></table>
      <p>&#9745; <span class="done">decide accent</span><br>&#9744; write the spec</p>
      <button class="btn">Primary button</button>
    </div>
  </div>
</body>
</html>
```

- [ ] **Step 2: Gitignore the dev harness**

Append to `.gitignore`:
```
# Local dev preview (not part of the theme)
preview.html
```

- [ ] **Step 3: Start the preview server and open the harness**

Use the preview MCP `preview_start` with command `python3 -m http.server 4599` and cwd the repo root, then `preview_eval`: `window.location.href = 'http://localhost:4599/preview.html'`.

- [ ] **Step 4: Screenshot to establish baseline**

`preview_screenshot`. Expected at this point: mostly unstyled/black — our variables are not defined yet, so `var(..., fallback)` and undefined vars leave default colors. This confirms the harness loads `theme.css` (no 404 in `preview_console_logs`). The page fills in as we add variables in later tasks.

- [ ] **Step 5: Commit**

```bash
git add .gitignore
git -c core.pager=cat commit -m "chore: add local preview harness (gitignored)" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 3: `:root` palette

**Files:**
- Modify: `theme.css` (append after header)

- [ ] **Step 1: Append the `:root` block**

```css
:root
{
    /* Deep backgrounds (dark only) */
    --pf-backdrop:   #15181e;
    --pf-sidebar:    #1b1f27;
    --pf-editor:     #242933;
    --pf-surface:    #2e3440;
    --pf-surface-hi: #3b4252;

    /* Pastel Dusk — accent family */
    --pf-teal:   #a8cbca;
    --pf-cyan:   #a2ceda;
    --pf-blue:   #9db6cf;
    --pf-indigo: #819dbe;

    /* Pastel Dusk — accent as RGB triplets (for rgba()) */
    --pf-teal-rgb: 168,203,202;
    --pf-cyan-rgb: 162,206,218;

    /* Text */
    --pf-text:     #d8dee9;
    --pf-emphasis: #eceff4;
    --pf-muted:    #8b93a3;
    --pf-faint:    #6c7484;

    /* Pastel Aurora — semantic / syntax */
    --pf-red:    #cf9aa0;
    --pf-orange: #d6a98f;
    --pf-yellow: #e6d2a8;
    --pf-green:  #b5ca9f;
    --pf-purple: #c3a9bd;
}
```

- [ ] **Step 2: Verify**

`preview_eval`: `window.location.reload()`, then `preview_screenshot`. Expected: the backdrop turns deep `#15181e` (page no longer pure black). Other elements still default because `.theme-dark` mappings come next.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git -c core.pager=cat commit -m "feat: add :root palette (deep bg, pastel Frost, pastel Aurora)" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 4: `body` global tokens

**Files:**
- Modify: `theme.css` (append after `:root`)

- [ ] **Step 1: Append the `body` block**

```css
body
{
    /* Accent (HSL) — drives --interactive-accent, selection handles, toggles */
    --accent-h: 178;
    --accent-s: 25%;
    --accent-l: 73%;

    /* Link / tag decorations */
    --link-decoration:                none;
    --link-decoration-hover:          underline;
    --link-external-decoration:       underline dotted;
    --link-external-decoration-hover: underline dotted;
    --tag-decoration:                 none;
    --tag-decoration-hover:           underline;
    --tag-padding-x:                  .55em;
    --tag-padding-y:                  .25em;

    /* Weights */
    --bold-weight:     600;
    --tab-font-weight: 600;

    /* Radius scale */
    --radius-s:          6px;
    --radius-m:          8px;
    --radius-l:          12px;
    --tab-radius:        var(--radius-m);
    --tab-radius-active: var(--radius-m);
    --modal-radius:      var(--radius-l);
    --checkbox-radius:   5px;
}
```

- [ ] **Step 2: Verify**

Reload + `preview_screenshot`. Expected: the button gains a soft teal fill (HSL accent) with dark text; code/tag/table corners are rounded. Headings/text still default until Tasks 5–6.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git -c core.pager=cat commit -m "feat: add body tokens (accent HSL, decorations, radius scale)" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 5: `.theme-dark` — backgrounds & text

**Files:**
- Modify: `theme.css` (append; this begins the `.theme-dark` block)

- [ ] **Step 1: Append `.theme-dark` opening with backgrounds + text**

```css
.theme-dark
{
    /* Backgrounds */
    --background-primary:               var(--pf-editor);
    --background-primary-alt:           var(--pf-editor);
    --background-secondary:             var(--pf-sidebar);
    --background-secondary-alt:         #20252e;
    --background-modifier-border:       var(--pf-surface);
    --background-modifier-border-hover: var(--pf-surface-hi);

    /* Text */
    --text-normal:    var(--pf-text);
    --text-muted:     var(--pf-muted);
    --text-faint:     var(--pf-faint);
    --text-on-accent: var(--pf-editor);
}
```
Note: leave this block **open** (no closing `}` yet) — Tasks 6–8 append more declarations, and Task 8 closes it. To keep the file valid between tasks, temporarily add a closing `}` at the end of each task's edit and remove it at the start of the next. (Simplest: close `}` now, and in Tasks 6–8 insert new lines *before* the final `}`.)

For clarity, close it now:
```css
}
```

- [ ] **Step 2: Verify**

Reload + `preview_screenshot`. Expected: editor pane is `#242933`, sidebar `#1b1f27`, body text soft white `#d8dee9`, muted labels grey. Panes already look like floating rounded cards on the deep backdrop.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git -c core.pager=cat commit -m "feat: theme-dark backgrounds and text" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 6: `.theme-dark` — headings, links, emphasis

**Files:**
- Modify: `theme.css` (insert before the closing `}` of `.theme-dark`)

- [ ] **Step 1: Insert headings + links + emphasis declarations**

Insert these lines immediately before the `}` that closes `.theme-dark`:
```css
    /* Headings — frost gradient (teal -> cyan -> blue -> indigo -> grey) */
    --h1-color:           var(--pf-teal);
    --h2-color:           var(--pf-cyan);
    --h3-color:           var(--pf-blue);
    --h4-color:           var(--pf-indigo);
    --h5-color:           var(--pf-muted);
    --h6-color:           var(--pf-faint);
    --inline-title-color: var(--pf-teal);

    /* Links — unified teal */
    --text-accent:               var(--pf-teal);
    --text-accent-hover:         var(--pf-cyan);
    --link-color:                var(--pf-teal);
    --link-color-hover:          var(--pf-cyan);
    --link-external-color:       var(--pf-teal);
    --link-external-color-hover: var(--pf-cyan);
    --link-unresolved-color:     var(--pf-muted);

    /* Emphasis */
    --bold-color:   var(--pf-emphasis);
    --italic-color: inherit;
```

- [ ] **Step 2: Verify**

Reload + `preview_screenshot`. Expected: inline title + H1 teal, H2 cyan, H3 blue, H4 indigo, H5/H6 grey; both links teal (external dotted-underlined); bold is crisp white `#eceff4`.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git -c core.pager=cat commit -m "feat: frost-gradient headings, unified teal links, white bold" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 7: `.theme-dark` — code, highlight, tags, checkboxes, selection

**Files:**
- Modify: `theme.css` (insert before the closing `}` of `.theme-dark`)

- [ ] **Step 1: Insert declarations**

Insert immediately before the `}` that closes `.theme-dark`:
```css
    /* Code */
    --code-normal:     var(--pf-cyan);
    --code-background: var(--pf-sidebar);

    /* Highlight (==mark==) */
    --text-highlight-bg: var(--pf-cyan);
    --text-highlight-fg: var(--pf-editor);

    /* Tags */
    --tag-color:            var(--pf-teal);
    --tag-background:       var(--pf-surface);
    --tag-background-hover: var(--pf-surface-hi);

    /* Checkboxes */
    --checkbox-color:              var(--pf-teal);
    --checkbox-color-hover:        var(--pf-cyan);
    --checkbox-border-color:       var(--pf-teal);
    --checkbox-border-color-hover: var(--pf-cyan);
    --checklist-done-color:        var(--pf-muted);

    /* Selection & active line */
    --cursor-line-background: rgba(var(--pf-teal-rgb), .08);
    --text-selection:         rgba(var(--pf-teal-rgb), .30);
    --flashing-background:    rgba(var(--pf-teal-rgb), .25);
```

- [ ] **Step 2: Verify**

Reload + `preview_screenshot`. Expected: inline + block code are soft cyan on `#1b1f27`; highlight is cyan with dark text; tags are teal pills; the done checklist item is muted/struck-through.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git -c core.pager=cat commit -m "feat: code, highlight, tags, checkboxes, selection" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 8: `.theme-dark` — semantic colors, tables, nav/chrome, graph (closes block)

**Files:**
- Modify: `theme.css` (insert before the closing `}` of `.theme-dark`)

- [ ] **Step 1: Insert declarations**

Insert immediately before the `}` that closes `.theme-dark`:
```css
    /* Semantic colours (callouts, syntax highlighting, graph) — pastel Aurora/Frost */
    --color-red:    var(--pf-red);
    --color-orange: var(--pf-orange);
    --color-yellow: var(--pf-yellow);
    --color-green:  var(--pf-green);
    --color-cyan:   var(--pf-cyan);
    --color-blue:   var(--pf-blue);
    --color-purple: var(--pf-purple);
    --color-red-rgb:    207,154,160;
    --color-green-rgb:  181,202,159;
    --color-blue-rgb:   157,182,207;

    /* Tables */
    --table-header-background:    var(--pf-sidebar);
    --table-header-background-hover: var(--pf-surface);
    --table-row-even-background:  #262c37;
    --table-row-odd-background:   #222730;
    --table-row-background-hover: var(--pf-surface);

    /* Nav / icons / window chrome */
    --nav-item-color-hover:        var(--pf-cyan);
    --nav-item-color-active:       var(--pf-teal);
    --nav-item-background-active:  rgba(var(--pf-teal-rgb), .15);
    --icon-color-hover:            var(--pf-teal);
    --icon-color-focused:          var(--pf-cyan);
    --titlebar-text-color-focused: var(--pf-teal);
    --tab-text-color-focused-active:         var(--pf-teal);
    --tab-text-color-focused-active-current: var(--pf-teal);

    /* Graph view */
    --graph-line:            var(--pf-surface);
    --graph-node:            var(--pf-text);
    --graph-node-tag:        var(--pf-teal);
    --graph-node-attachment: var(--pf-green);
```

- [ ] **Step 2: Verify the `.theme-dark` block is well-formed**

Run:
```bash
cd "/Users/jannik/Documents/Claude Code/obsidian-theme"
awk '{o+=gsub(/{/,"{"); c+=gsub(/}/,"}")} END{print "braces open:",o," close:",c}' theme.css
```
Expected: `open` equals `close`. Reload + `preview_screenshot`: callout title/border are pastel blue; the active sidebar item shows a teal-tinted rounded background; table header text is teal.

- [ ] **Step 3: Commit**

```bash
git add theme.css
git -c core.pager=cat commit -m "feat: semantic colours, tables, nav/chrome, graph" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 9: Structural CSS — rounded floating panes, highlight fg, rounded media

**Files:**
- Modify: `theme.css` (append after the `.theme-dark` block)

- [ ] **Step 1: Append structural rules**

```css
/* ── Rounded floating panes ───────────────────────────────────────── */
/* Backdrop shows between leaves so each pane reads as a rounded card. */
.theme-dark .workspace-split.mod-root { background: var(--pf-backdrop); }

.theme-dark .workspace-tabs .workspace-leaf,
.theme-dark .workspace-tab-container {
    border-radius: var(--radius-l);
}
.theme-dark .workspace-tabs .workspace-leaf { margin: 3px; overflow: hidden; }

/* Keep ==highlight== foreground readable in both reading and live-preview. */
.theme-dark .markdown-rendered mark,
.theme-dark .cm-highlight,
.theme-dark span.cm-highlight {
    color: var(--text-highlight-fg);
}

/* Rounded images and embeds. */
.theme-dark .markdown-rendered img,
.theme-dark .markdown-rendered .internal-embed,
.theme-dark .cm-s-obsidian .image-embed img {
    border-radius: var(--radius-m);
}
```

- [ ] **Step 2: Verify (harness sanity only)**

Reload + `preview_screenshot`. The `.workspace-*` selectors don't exist in the harness (Obsidian-only), so visuals are unchanged here — this step just confirms no CSS parse breakage: check `preview_console_logs` shows no stylesheet errors, and re-run the brace-balance `awk` from Task 8 Step 2 (must stay balanced).

- [ ] **Step 3: Commit**

```bash
git add theme.css
git -c core.pager=cat commit -m "feat: rounded floating panes, highlight fg, rounded media" -m "Supported by Claude Opus 4.8"
git push origin main
```

> **In-app follow-up (Task 11):** the exact `.workspace-*` selectors must be confirmed against the running Obsidian DOM; adjust there if panes don't round/gap as expected.

---

## Task 10: Showcase note for in-Obsidian verification

**Files:**
- Create: `sample/Pastel Dusk Showcase.md`

- [ ] **Step 1: Create the showcase note**

````markdown
# Pastel Dusk Showcase

Body text with an [[internal link]], an [external link](https://obsidian.md),
**bold**, *italic*, ==a highlight==, and `inline code`. A #nord #theme tag pair.

## Heading two
### Heading three
#### Heading four
##### Heading five
###### Heading six

> [!note] Note callout
> Info callouts should be pastel blue.

> [!warning] Warning callout
> Warnings should be pastel amber.

> [!success] Success callout
> Success should be pastel green.

```js
const greet = (name) => `hi ${name}`;
console.log(greet("Nord"));
```

| Decision   | Value     |
| ---------- | --------- |
| Accent     | #a8cbca   |
| Background  | #242933   |

- [x] decide accent
- [ ] write the spec
- [ ] ship it

Text selection, the active line, and the graph view (open it) should all read teal.
````

- [ ] **Step 2: Commit**

```bash
git add "sample/Pastel Dusk Showcase.md"
git -c core.pager=cat commit -m "docs: add Pastel Dusk showcase note for verification" -m "Supported by Claude Opus 4.8"
git push origin main
```

---

## Task 11: In-Obsidian verification + finalize

**Files:**
- Modify: `README.md` (only if install steps need tweaking)
- Modify: `theme.css` (only if in-app verification requires selector fixes)

- [ ] **Step 1: Install into a vault**

Tell the user to copy the theme into a vault (or use an existing one):
```bash
# Example — replace <VAULT> with a real vault path:
mkdir -p "<VAULT>/.obsidian/themes/Pastel Dusk"
cp theme.css manifest.json "<VAULT>/.obsidian/themes/Pastel Dusk/"
```
Then in Obsidian: **Settings → Appearance → Themes → Pastel Dusk**, and open `sample/Pastel Dusk Showcase.md`.

- [ ] **Step 2: Verify against the spec**

Ask the user to confirm (or share screenshots of) reading + editing views:
- Backgrounds: editor `#242933`, sidebar `#1b1f27`, panes rounded & floating on the deep backdrop.
- Headings frost-gradient; inline title teal.
- Links teal (external dotted); bold white; highlight cyan.
- Inline + fenced code soft cyan on `#1b1f27`.
- Callouts pastel (blue/amber/green); table striped with teal header; checkboxes teal.
- Active line + selection + graph nodes/tags teal.

- [ ] **Step 3: Fix any gaps**

If the floating panes don't round/gap correctly, inspect the leaf DOM in Obsidian (Developer Tools) and correct the `.workspace-*` selectors in the structural section, then re-verify. Apply any other color/selector corrections surfaced by the user.

- [ ] **Step 4: Finalize README + commit**

Confirm `README.md` install steps match Step 1 (folder name "Pastel Dusk"). Make any wording fixes, then:
```bash
git add -A
git -c core.pager=cat commit -m "fix: in-app verification corrections; finalize v0.1.0" -m "Supported by Claude Opus 4.8"
git push origin main
```

- [ ] **Step 5: Tag the release (optional)**

```bash
git tag 0.1.0 && git push origin 0.1.0
```

---

## Self-Review

**Spec coverage:**
- Dark only → Task 1 (no light block authored), Task 5+ only `.theme-dark`. ✓
- Deep backgrounds + backdrop → Tasks 3, 5, 9. ✓
- Unified pastel teal accent (links/buttons/active/title/tags) → Tasks 3,4,6,7,8. ✓
- Frost-gradient headings → Task 6. ✓
- Pastel Dusk + Aurora palette → Tasks 3, 8. ✓
- Rounding (radius scale, floating panes, tabs/inputs/embeds/media) → Tasks 4, 9. ✓
- Kept-stock components recolored (callouts/tables/checkboxes/graph/syntax) → Tasks 7, 8. ✓
- Attribution/licence header → Task 1. ✓
- Verification (preview harness + showcase note in-app) → Tasks 2, 10, 11. ✓
- manifest "Pastel Dusk" → Task 1. ✓

**Placeholder scan:** No TBD/TODO; every CSS/JSON/markdown step contains complete content. The one deferred item (exact `.workspace-*` selectors) is an explicit in-app verification step with a concrete fix procedure (Task 9 note, Task 11 Step 3), not a content gap.

**Type/name consistency:** Variable names are consistent across tasks (`--pf-teal`, `--pf-teal-rgb`, `--pf-editor`, `--pf-sidebar`, etc.). Harness class names in Task 2 match the variables exercised in Tasks 3–8. Folder name "Pastel Dusk" consistent in manifest (Task 1), showcase path (Task 10), install path (Task 11), README.

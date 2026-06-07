# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

This is **not** an application — it's a **Claude Code Skill** named `easy-wireframe` (defined in `SKILL.md`). Its purpose: turn a description of an app ("what app, which pages, how they click through") into a single zero-dependency HTML file — multiple iPhone-sized (375×812) page cards laid out horizontally with arrows annotating page-to-page transitions. Style is locked to minimalist black/white/grey, iOS sizing, Apple HIG typography.

There is no build, test, or lint step. The deliverable is a hand-assembled `.html` file.

## How the files relate (read in this order when generating)

1. `SKILL.md` — the skill manifest + workflow. The authoritative process: clarify needs → copy `template.html` → assemble pages → self-check.
2. `design-system.md` — the "measurement standard." Every number in `template.html`'s `<style>` is locked to this table (colors, HIG font sizes, dimensions, Lucide icons, arrow SVG). Read it to *understand* values; **never edit `<style>` to change them.**
3. `template.html` — the skeleton to copy as the output file. Already contains all CSS + annotation JS + screenshot JS. Has `{{APP_NAME}}` placeholders and an empty `.flow` with a placeholder comment to fill.
4. `components.md` — copy-paste HTML blocks (navbar / list / chat / stats / arrow / branch layout) that drop into `.flow`. Depend on template CSS; only swap text, `data-lucide` icon names, numbers.
5. `example.html` — the golden reference (轻记·习惯打卡, 4 pages). Demonstrates list/detail/stats pages, branch layout, switches. Consult when unsure how to write a page or component.

## Generating a wireframe (the actual task)

- Copy `template.html` → output file (e.g. `prototype.html`). Replace `{{APP_NAME}}`. Fill the `.flow` placeholder by alternating `.stage` (a page) → `.arrow` (a transition) → `.stage` → …
- Each `.stage` gets a unique `.stage-label` (① ② ③ …). Each `.arrow`'s `.tag` states the click action ("点击「景区伴游」").
- **Branching** (one page → multiple routes): use `.flow.branched` with `.stage.origin`, an empty `.fork`, and `.branch` rows each carrying `data-tag`. The template's `drawForks()` JS draws connector polylines/arrows/labels from real DOM coordinates — author only adds the classes + `data-tag`, draws no lines manually.

## Hard rules (this design system was iterated heavily — violating these breaks layout)

- **Do not edit any number or `:root` variable in `template.html`'s `<style>`.** Sizes/fonts/spacing are proportionally locked. To change style, go back to `design-system.md`, understand it, then adjust holistically.
- Layout is `.canvas{width:max-content;margin:0 auto}` centered + `.flow{flex-wrap:nowrap}` horizontal; page overflows scroll horizontally. **Never reintroduce `flex-wrap:wrap` or "rotate arrow 90° on narrow screens" responsive behavior** — that was the original source of layout breakage.
- `.stage` and `.arrow` must keep `flex-shrink:0`. `.screen` uses `min-height:812px` (grows down with content, never clips); inner containers (`.body`/`.chat-stream`/`.stat-scroll`) must not add `overflow:hidden`.
- Only use Lucide icon names that actually exist — a misspelled/nonexistent name renders blank silently (no error). Prefer the curated list in `design-system.md` §4; verify any new name on lucide.dev first.

## Built-in template features (keep as-is, don't reimplement)

The template's JS already provides: annotation mode (pins + notes, exportable, stored in `localStorage` namespaced by filename so each file starts clean), full-canvas 2× PNG screenshot via html2canvas (targets `.canvas` so horizontally-overflowing pages are fully captured), editable title/subtitle persisted to `localStorage`, and `drawForks()` branch-connector rendering (redraws on resize).

## Previewing output

Output HTML is zero-dependency — open directly in a browser. For headless verification (flows are wide, give a wide window; branch connectors need 2–3s to render):

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu --hide-scrollbars --window-size=2400,1600 \
  --default-background-color=ffffffff --screenshot=/tmp/preview.png "file://$PWD/输出文件.html"
```

---
name: better-layout
description: Layout structure for web interfaces, from grouping and alignment to reading order, progressive disclosure, and adaptive breakpoints. Use when structuring a page or component, spacing or aligning controls, deciding what collapses at small sizes, handling RTL layout direction, or reviewing frontend code for layout. Triggers on layout, spacing, alignment, grouping, negative space, whitespace, visual hierarchy, reading order, progressive disclosure, breakpoints, responsive layout, container queries, safe area, full-bleed, edge-to-edge, layout margins, RTL layout, logical properties.
---

# Layout that communicates structure

Layout communicates before a single word is read: position, spacing, and alignment carry hierarchy on their own, and generous space beats decoration. A good layout also survives stress — resize it, translate it, mirror it for RTL, and it should still hold together. Apply these principles when building or reviewing UI code, and express every change in the project's existing styling system (Tailwind, plain CSS, CSS-in-JS); never introduce a second styling approach.

Hit-area sizes and focus behavior are covered by the `better-accessibility` skill; visual polish (radius, shadows, animation) by the `better-ui` skill; line length and text spacing by the `better-typography` skill.

## Quick Reference

| Category | When to Use |
| --- | --- |
| [Grouping & Alignment](grouping-and-alignment.md) | Space vs separators, alignment edges, logical properties, importance ordering |
| [Spacing & Adaptivity](spacing-and-adaptivity.md) | Spacing between targets, layout margins, progressive disclosure, full-bleed content, breakpoints, i18n growth |

## Core Principles

### 1. Group with Space, Not Lines

Negative space is the primary grouping tool; background shapes second; separator lines last, only where space alone can't carry the structure. The gap between groups must be at least 2× the gap within a group (`8px` intra-group → `16px`+ inter-group), or the grouping reads as noise.

### 2. Keep Controls Distinct from Content

Interactive elements must look interactive: a background shape, a border, or a consistent placement zone. Never style a control identically to adjacent static text.

### 3. Align to Shared Edges

Pick alignment edges and stick to them; every stray edge reads as noise. Indent exactly one step (`16px`) to signal subordination. Use logical properties (`padding-inline-start`, `margin-inline-end`), never physical left/right, so RTL mirrors for free.

### 4. Order by Importance

The most important content sits near the top and the leading edge; reading order flows top-to-bottom, leading-to-trailing. Think in leading/trailing, not left/right.

### 5. Hint at Hidden Content

Progressive disclosure needs a visible affordance: let the next item peek `16–32px` past the scroll edge, or show a disclosure control. Content hidden with zero cue may as well not exist.

### 6. Breathing Room Between Targets

Keep at least `12px` between adjacent bordered or filled controls, and `24px` of clearance around borderless text- and icon-only controls. Hit-area sizes themselves are covered by the `better-accessibility` skill.

### 7. Inset Buttons from the Edges

No edge-to-edge, full-width buttons pressed against the viewport. Keep buttons inside the layout margins — at least `16px` inline margin on mobile — with a visible radius.

### 8. Content Bleeds, Controls Float

Backgrounds and media extend to the viewport edges; controls and text stay inside the layout margins and safe areas (`env(safe-area-inset-*)`). Sticky chrome floats above the content layer, it doesn't dam it.

### 9. Hold Structure Until It Breaks

Breakpoints come from the content, not device presets. Keep the expanded layout as long as it genuinely fits and collapse late; prefer container queries for component-level adaptation. Test the smallest and largest sizes first.

### 10. Plan for Growth and Clipping

Translated strings run 30–40% longer: no fixed widths or heights on text containers, and let rows wrap. Never park critical actions where resizing or scrolling clips them; keep primary actions in stable chrome.

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| Separator line where spacing would do | Remove the line, double the gap between groups |
| `margin-left` / `padding-right` in a localizable layout | `margin-inline-start` / `padding-inline-end` |
| Full-width edge-to-edge button | Inset within layout margins (`mx-4` minimum on mobile) |
| Carousel/scroller that looks complete | Let the next item peek `16–32px` past the edge |
| Adjacent controls with no gap | `12px` minimum between bordered controls, `24px` around borderless ones |
| Breakpoints at 768/1024 because they're the defaults | Break where the content actually stops fitting |
| Fixed-width text container sized to English strings | `max-width` + wrapping; translations run 30–40% longer |
| Primary action at the clip-prone bottom of a pane | Sticky positioning or stable chrome with safe-area padding |

## Review Output Format

Always present changes as a markdown table with **Before** and **After** columns. Include every change you made, not just a subset. Never list findings as separate "Before:" / "After:" lines outside of a table. Group changes by principle using a heading above each table, and keep each row focused on a single diff so the reader can scan the whole list quickly.

### Example

#### Group with space, not lines
| Before | After |
| --- | --- |
| `border-b` on every settings row | Removed borders; `space-y-2` within groups, `space-y-8` between groups |
| `<hr>` between form sections | Replaced with `mt-10` on each section heading |

#### Align to shared edges
| Before | After |
| --- | --- |
| Card text at `pl-4`, card icon at `pl-3` | Both aligned to the same `pl-4` edge |
| `margin-left: 16px` on nested list | `margin-inline-start: 16px` |

Rows should cite the specific file and the specific property that changed when it isn't obvious from the snippet. If a principle was reviewed but nothing needed to change, omit that table entirely: empty tables add noise.

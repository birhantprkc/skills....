---
name: better-layout
description: Layout structure for web interfaces. Use when structuring a page or component, deciding what collapses at small sizes, or reviewing frontend code for layout. Triggers on layout, spacing, alignment, grouping, whitespace, visual hierarchy, reading order, progressive disclosure, breakpoints, container queries, safe area, full-bleed, layout margins, RTL layout, logical properties.
---

# Layout

Position, spacing, and alignment carry hierarchy before a word is read. This skill is the reference for building that structure and for stress-testing it: resize it, translate it, mirror it for RTL.

Write every fix in the styling system the project already uses. Treat the numbers below as starting points for interfaces with no established density or spacing system. Keep deliberate platform chrome, compact professional tools, and project tokens where they still pass the stress tests.

Hit areas and focus behavior belong to `better-accessibility`. Radius, shadows, and animation belong to `better-ui`. Line length and text spacing belong to `better-typography`.

## Quick reference

| Category | When to Use |
| --- | --- |
| [Grouping & Alignment](grouping-and-alignment.md) | Space vs separators, alignment edges, logical properties, importance ordering |
| [Spacing & Adaptivity](spacing-and-adaptivity.md) | Spacing between targets, layout margins, progressive disclosure, full-bleed content, breakpoints, i18n growth |

## Core principles

### Group with space, not lines

Negative space is the primary grouping tool; background shapes second; separator lines last, only where space alone can't carry the structure. The gap between groups must be at least 2× the gap within a group (`8px` intra-group → `16px`+ inter-group), or the grouping reads as noise.

### Keep controls distinct from content

Interactive elements must look interactive: a background shape, a border, or a consistent placement zone. Never style a control identically to adjacent static text.

### Align to shared edges

Pick alignment edges and stick to them; every stray edge reads as noise. Use one project spacing step for each level of subordination (`16px` is a useful default). Use logical properties (`padding-inline-start`, `margin-inline-end`) for direction-dependent layout; reserve physical left/right for genuinely physical geometry.

### Order by importance

The most important content sits near the top and the leading edge; reading order flows top-to-bottom, leading-to-trailing. Think in leading/trailing, not left/right.

### Hint at hidden content

Progressive disclosure needs a visible affordance. Use the project's established cue; without one, let the next item peek `16–32px` past the scroll edge or show a disclosure control. Content hidden with zero cue may as well not exist.

### Breathing room between targets

Without an established density system, start with `12px` between adjacent bordered or filled controls and `24px` of clearance around borderless text- and icon-only controls. Compact layouts may use less when `better-accessibility` hit areas do not overlap and the controls remain visually distinct.

### Inset buttons from the edges

In content layouts, keep full-width buttons inside the layout margins (start near `16px` inline on mobile) with a visible radius. Edge-to-edge actions are acceptable when they intentionally follow established platform or application chrome, account for safe areas, and remain distinguishable from system UI.

### Content bleeds, controls float

Backgrounds and media extend to the viewport edges; controls and text stay inside the layout margins and safe areas (`env(safe-area-inset-*)`). Sticky chrome floats above the content layer, it doesn't dam it.

### Hold structure until it breaks

Breakpoints come from the content, not device presets. Keep the expanded layout as long as it genuinely fits and collapse late; prefer container queries for component-level adaptation. Test the smallest and largest sizes first.

### Plan for growth and clipping

Plan for substantial and language-dependent string growth rather than relying on a universal percentage: no fixed widths or heights on text containers, and let rows wrap. Never park critical actions where resizing or scrolling clips them; keep them reachable in the normal flow or stable chrome appropriate to the product.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| Separator line where spacing would do | Remove the line, double the gap between groups |
| `margin-left` / `padding-right` in a localizable layout | `margin-inline-start` / `padding-inline-end` |
| Content-layout button accidentally touches the viewport | Inset within the project margins; preserve intentional platform chrome |
| Carousel/scroller that looks complete | Let the next item peek `16–32px` past the edge |
| Adjacent controls merge or expanded hit areas overlap | Increase the gap using the project scale; use `12px`/`24px` as starting points |
| Breakpoints at 768/1024 because they're the defaults | Break where the content actually stops fitting |
| Fixed-width text container sized to one language | `max-width` + wrapping; test pseudo-localization and representative locales |
| Primary action at the clip-prone bottom of a pane | Sticky positioning or stable chrome with safe-area padding |

## Reporting

A standalone layout review is finished when every confirmed finding is reported with verification and a verdict. Call the Skill tool with `better-interface` for the table structure and the verdict ladder; the two things below are the ones specific to layout.

**Severity.** `HIGH` blocks content or an action at a supported viewport. `MEDIUM` harms hierarchy, reading order, or adaptability. `LOW` is isolated alignment or spacing polish.

**Verification.** Every supported viewport width, reading order, 200% zoom, and the RTL mirror.

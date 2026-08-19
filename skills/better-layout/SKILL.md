---
name: better-layout
description: Layout structure for web interfaces. Use when structuring a page or component, deciding what collapses at small sizes, or reviewing frontend code for layout. Triggers on layout, spacing, alignment, grouping, whitespace, visual hierarchy, reading order, progressive disclosure, breakpoints, container queries, safe area, full-bleed, layout margins, RTL layout, logical properties.
---

# Layout

Position, spacing, and alignment carry hierarchy before a word is read. This skill is the reference for building that structure and for stress-testing it: resize it, translate it, mirror it for RTL.

Write every fix in the styling system the project already uses. Treat the numbers below as starting points for interfaces with no established density or spacing system. Keep deliberate platform chrome, compact professional tools, and project tokens where they still pass the stress tests.

Hit areas and focus behavior belong to `better-accessibility`. Radius, shadows, and animation belong to `better-ui`. Line length and text spacing belong to `better-typography`.

## Group with space, not lines

Space groups first. Background shapes second. Separator lines last, and only where space alone can't carry the structure. The gap between groups must be at least 2× the gap within a group (`8px` intra-group to `16px`+ inter-group), or the grouping reads as noise. Alignment edges and importance ordering are in [grouping-and-alignment.md](grouping-and-alignment.md).

## Keep controls distinct from content

Give every interactive element a background shape, a border, or a consistent placement zone. A control styled like the static text beside it is not readable as a control.

## Align to shared edges

Pick alignment edges and stick to them; every stray edge reads as noise. Use one project spacing step per level of subordination, where `16px` is a useful default.

Use logical properties for direction-dependent layout: `padding-inline-start`, `margin-inline-end`. Reserve physical left and right for genuinely physical geometry.

## Order by importance

The most important content sits near the top and the leading edge. Reading order flows top-to-bottom, leading-to-trailing. Think in leading and trailing, not left and right.

## Hint at hidden content

Progressive disclosure needs a visible affordance. Use the project's established cue. Without one, let the next item peek `16–32px` past the scroll edge, or show a disclosure control. Content hidden with zero cue may as well not exist.

## Breathing room between targets

Without an established density system, start with `12px` between adjacent bordered or filled controls, and `24px` of clearance around borderless text- and icon-only controls. Compact layouts may use less, as long as `better-accessibility` hit areas don't overlap and the controls stay visually distinct. Layout margins and breakpoint recipes are in [spacing-and-adaptivity.md](spacing-and-adaptivity.md).

## Inset buttons from the edges

In content layouts, keep full-width buttons inside the layout margins with a visible radius. Start near `16px` inline on mobile. Edge-to-edge actions work when they deliberately follow established platform or application chrome, account for safe areas, and stay distinguishable from system UI.

## Content bleeds, controls float

Backgrounds and media extend to the viewport edges. Controls and text stay inside the layout margins and safe areas (`env(safe-area-inset-*)`). Sticky chrome floats above the content layer rather than blocking it.

## Hold structure until it breaks

Breakpoints come from the content, not from device presets. Keep the expanded layout as long as it genuinely fits, and collapse late. Prefer container queries for component-level adaptation. Test the smallest and largest sizes first.

## Plan for growth and clipping

Translated strings grow, and short strings grow proportionally more than long ones, so a one-word button label is the riskiest thing on the screen. Put no fixed width or height on a text container, and let rows wrap. Test with pseudo-localization and at least one representative locale rather than budgeting a single percentage.

Never park a critical action where resizing or scrolling clips it. Keep it in the normal flow, or in stable chrome suited to the product.

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

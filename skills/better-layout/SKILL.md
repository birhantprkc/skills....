---
name: better-layout
description: Layout structure for web interfaces. Use when structuring a page or component, deciding what collapses at small sizes, or reviewing frontend code for layout. Triggers on layout, spacing, alignment, grouping, whitespace, visual hierarchy, reading order, progressive disclosure, breakpoints, container queries, safe area, full-bleed, layout margins, RTL layout, logical properties.
---

# Layout

Position, spacing, and alignment carry hierarchy before a word is read. This skill is the reference for building that structure and for stress-testing it: resize it, translate it, mirror it for RTL.

Write every fix in the styling system the project already uses. Treat the numbers below as starting points for interfaces with no established density or spacing system, and use the numbers as written rather than a familiar-looking substitute. Keep deliberate platform chrome, compact professional tools, and project tokens where they still pass the stress tests.

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

## Before you finish

| Mistake | Fix |
| --- | --- |
| `margin-left` / `padding-right` in a localizable layout | `margin-inline-start` / `padding-inline-end` |
| Content-layout button accidentally touches the viewport | Inset within the project margins; preserve intentional platform chrome |
| Breakpoints at 768/1024 because they're the defaults | Break where the content actually stops fitting |
| Fixed-width text container sized to one language | `max-width` + wrapping; test pseudo-localization and representative locales |
| Primary action at the clip-prone bottom of a pane | Sticky positioning or stable chrome with safe-area padding |

## Reporting

**Severity.** `HIGH` blocks content or an action at a supported viewport. `MEDIUM` harms hierarchy, reading order, or adaptability. `LOW` is isolated alignment or spacing polish.

**Verification.** Without a browser: logical properties in place of physical ones, container and media queries against the supported viewport list, and DOM order against the intended reading order. With one: every supported width, 200% zoom, and the RTL mirror. Report every check you could not run as `Not verified`.

**Format.** Group findings under the principle each violates, ordered by severity within a group:

| Severity | Location | Before | After | Why |
| --- | --- | --- | --- | --- |

`Location` cites `path/to/file:line`. `Before` and `After` share one row: the current implementation, then an actionable replacement. `Why` names the principle and the user impact. One row per root cause, listing every location it appears in.

End with a verdict. `Block` when any `HIGH` remains, meaning do not ship until it is fixed. `Approve` otherwise, leaving any `MEDIUM` and `LOW` findings in the table as work to do. Never `Approve` coverage you did not inspect. With nothing to report, state "No actionable layout findings", report verification, and end with `Approve`.

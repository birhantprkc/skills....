# Spacing & Adaptivity

Space between controls, margins against the viewport, and layouts that survive resizing and translation.

## Breathing Room Between Targets

Controls placed too close together get mis-tapped and read as one unit. Minimum clearances:

| Between | Minimum |
| --- | --- |
| Adjacent bordered/filled controls (buttons, inputs) | `12px` |
| Around borderless controls (text buttons, icon buttons) | `24px` |
| Unrelated control groups | `24px`+ (2× the intra-group gap) |

Borderless controls need more clearance because nothing marks where one target ends and the next begins — the space itself is the boundary.

```html
<!-- Good: bordered buttons at 12px, icon buttons given room -->
<div class="flex gap-3">
  <button class="rounded-lg border px-4 py-2">Cancel</button>
  <button class="rounded-lg bg-blue-600 px-4 py-2 text-white">Save</button>
</div>

<!-- Bad: three borderless icon buttons packed at 4px -->
<div class="flex gap-1">
  <button><TrashIcon /></button>
  <button><ArchiveIcon /></button>
  <button><ShareIcon /></button>
</div>
```

Hit-area sizes (44×44px touch, 40×40px desktop) and pseudo-element expansion are covered by the `better-accessibility` skill; these clearances are in addition, so expanded hit areas never overlap.

## Inset Buttons from the Edges

Buttons pressed edge-to-edge against the viewport look like system chrome and clip against curved corners and gesture zones. Keep them inside the layout margins:

```css
/* Good: inset action bar */
.action-bar {
  padding-inline: 16px;
  padding-bottom: calc(16px + env(safe-area-inset-bottom));
}
.action-bar button { width: 100%; border-radius: 12px; }

/* Bad: button glued to all three edges */
.action-bar button {
  width: 100vw;
  border-radius: 0;
  position: fixed;
  bottom: 0;
}
```

Minimum `16px` inline margin on mobile; the button can still span the full content width inside those margins.

## Content Bleeds, Controls Float

The two layers behave differently at the edges:

- **Content layer** — backgrounds, hero media, and scrollable lists extend to the viewport edges. A page background that stops short of the edge looks like a rendering bug.
- **Control layer** — text and controls stay inside the layout margins and safe areas, floating above the content.

```css
/* Good: full-bleed media inside a constrained article */
.article {
  display: grid;
  grid-template-columns: 1fr min(65ch, calc(100% - 48px)) 1fr;
}
.article > * { grid-column: 2; }
.article > .full-bleed { grid-column: 1 / -1; }
```

Sticky headers and floating action buttons account for safe areas:

```css
.fab {
  position: fixed;
  inset-inline-end: calc(16px + env(safe-area-inset-right));
  bottom: calc(16px + env(safe-area-inset-bottom));
}
```

## Hold Structure Until It Breaks

Breakpoints belong to the content, not the device catalog:

- Break where the layout actually stops fitting — when the sidebar squeezes the content below its minimum measure, when the card grid drops below a usable column width — not at `768px` because a preset says so.
- Collapse late. A layout that keeps its expanded structure as long as it genuinely fits stays stable and familiar; premature collapsing throws away space users paid for.
- Prefer **container queries** for components: a card should adapt to the column it's in, not to the viewport.

```css
/* Good: component adapts to its container */
.card-list { container-type: inline-size; }
@container (max-width: 400px) {
  .card { grid-template-columns: 1fr; }
}

/* Bad: viewport media query breaks the card inside a narrow sidebar */
@media (max-width: 768px) {
  .card { grid-template-columns: 1fr; }
}
```

Test order: the smallest supported size and the largest first — those break first — then the sizes in between.

## Plan for Growth and Clipping

Layouts fail in two directions: content grows, and viewports shrink.

**Translated strings run 30–40% longer** than English (German famously more). Rules:

- No fixed widths sized to English labels; use `max-width` plus wrapping.
- No fixed heights on text containers; use `min-height` if a floor is needed.
- Buttons size themselves from their label (`padding-inline`), never a hardcoded width.
- Test with pseudo-localization or a long-string locale before shipping.

```css
/* Good: label defines the size */
.button { padding-inline: 16px; white-space: nowrap; }

/* Bad: German will overflow or truncate */
.button { width: 96px; overflow: hidden; }
```

**Clipping:** never park critical actions where they can be cut off — the bottom edge of a resizable pane, below the fold of a fixed-height modal, behind an expanding keyboard. Keep primary actions in stable chrome: a sticky footer with safe-area padding, or the top of the view. If a modal's content scrolls, its action row doesn't.

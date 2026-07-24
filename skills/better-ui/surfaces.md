# Surfaces

Border radius, optical alignment, shadows, and image outlines.

## Concentric Border Radius

When nesting rounded elements, the outer radius must equal the inner radius plus the padding between them:

```
outerRadius = innerRadius + padding
```

This rule is most useful when nested surfaces are close together. If padding is larger than `24px`, treat the layers as separate surfaces and choose each radius independently instead of forcing strict concentric math.

### Example

```css
/* Good: concentric radii */
.card {
  border-radius: 20px; /* 12 + 8 */
  padding: 8px;
}
.card-inner {
  border-radius: 12px;
}

/* Bad: same radius on both */
.card {
  border-radius: 12px;
  padding: 8px;
}
.card-inner {
  border-radius: 12px;
}
```

### Tailwind Example

```tsx
// Good: outer radius accounts for padding
<div className="rounded-2xl p-2">       {/* 16px radius, 8px padding */}
  <div className="rounded-lg">          {/* 8px radius = 16 - 8 ✓ */}
    ...
  </div>
</div>

// Bad: same radius on both
<div className="rounded-xl p-2">
  <div className="rounded-xl">          {/* same radius, looks off */}
    ...
  </div>
</div>
```

Mismatched border radii on closely nested surfaces is a common source of visual tension. Calculate concentrically when the layers share a visible, even inset; preserve an established component token when the layers are independent or the padding is intentionally asymmetric.

## Optical Alignment

When geometric centering looks off, align optically instead.

### Buttons with Text + Icon

When an icon makes otherwise symmetric padding look unbalanced, use slightly less padding on the icon side. A useful starting point is:
`icon-side padding = text-side padding - 2px`.

```css
/* Good: less padding on icon side */
.button-with-icon {
  padding-inline-start: 16px;
  padding-inline-end: 14px; /* trailing icon side = text side - 2px */
}

/* Bad: equal padding looks like icon is pushed too far right */
.button-with-icon {
  padding-inline: 16px;
}
```

```tsx
// Tailwind
<button className="ps-4 pe-3.5 flex items-center gap-2">
  <span>Continue</span>
  <ArrowRightIcon />
</button>
```

### Play Button Triangles

Play icons are triangular and their geometric center is not their visual center. Shift slightly right:

```css
/* Good: optically centered */
.play-button svg {
  transform: translateX(2px); /* physical correction to the glyph itself */
}

/* Bad: geometrically centered but looks off */
.play-button svg {
  /* no adjustment */
}
```

### Asymmetric Icons (Stars, Arrows, Carets)

Some icons have uneven visual weight. The best fix is adjusting the SVG directly so no extra margin/padding is needed in the component code.

```tsx
// Best: fix in the SVG itself
// Adjust the viewBox or path to visually center the icon

// Fallback: adjust with margin
<span className="translate-x-px">
  <StarIcon />
</span>
```

## Shadows Instead of Borders

For **buttons, cards, and containers** whose border exists only for depth or elevation, consider the project's established shadow treatment. Transparent shadows can adapt across related surfaces, but they are not universally clearer than borders and still need verification on every supported background.

**Do not apply this to dividers** (`border-b`, `border-t`, side borders) or any border whose purpose is layout separation rather than element depth. Those should stay as borders.

### Shadow as Border (Light Mode)

The shadow is comprised of three layers. The first acts as a 1px border ring, the second adds subtle lift, and the third provides ambient depth:

```css
:root {
  --shadow-border:
    0px 0px 0px 1px oklch(0 0 0 / 0.06),
    0px 1px 2px -1px oklch(0 0 0 / 0.06),
    0px 2px 4px 0px oklch(0 0 0 / 0.04);
  --shadow-border-hover:
    0px 0px 0px 1px oklch(0 0 0 / 0.08),
    0px 1px 2px -1px oklch(0 0 0 / 0.08),
    0px 2px 4px 0px oklch(0 0 0 / 0.06);
}
```

### Shadow as Border (Dark Mode)

In dark mode, simplify to a single white ring, since layered depth shadows aren't visible on dark backgrounds:

```css
/* Dark mode: adapt to whatever setup the project uses
   (prefers-color-scheme, class, data attribute, etc.) */
--shadow-border: 0 0 0 1px oklch(1 0 0 / 0.08);
--shadow-border-hover: 0 0 0 1px oklch(1 0 0 / 0.13);
```

### Usage with Hover Transition

Apply the variable and add `transition-[box-shadow]` for a smooth hover:

```css
.card {
  box-shadow: var(--shadow-border);
  transition-property: box-shadow;
  transition-duration: 150ms;
  transition-timing-function: ease-out;
}

.card:hover {
  box-shadow: var(--shadow-border-hover);
}
```

### When to Use Shadows vs. Borders

| Use shadows | Use borders |
| --- | --- |
| Cards, containers with depth | Dividers between list items |
| Buttons with bordered styles | Table cell boundaries |
| Elevated elements (dropdowns, modals) | Form input outlines (for accessibility) |
| Elements on varied backgrounds | Hairline separators in dense UI |
| Hover/focus states for lift effect | |

## Image Outlines

Add an inset `1px` outline only when an image edge disappears into the surrounding surface and the project has no canonical image-frame treatment. Skip transparent artwork, logos, edge-to-edge media, and components that already provide separation.

### Color recipe

- Prefer the project's neutral separator token when it remains visually neutral across supported surfaces.
- Without a suitable token, use pure black at 10% in light mode (`oklch(0 0 0 / 0.1)`) and pure white at 10% in dark mode (`oklch(1 0 0 / 0.1)`).
- Do not use an accent color: the outline is a separator, not a themed state.

### Light Mode

```css
img {
  outline: 1px solid oklch(0 0 0 / 0.1);
  outline-offset: -1px; /* draw the ring just inside the image edge */
}
```

### Dark Mode

```css
img {
  outline: 1px solid oklch(1 0 0 / 0.1);
  outline-offset: -1px;
}
```

### Tailwind with Dark Mode

```tsx
<img
  className="outline outline-1 -outline-offset-1 outline-black/10 dark:outline-white/10"
  src={src}
  alt={alt}
/>
```

Use these black/white values when the project has no canonical neutral separator token.

**Why outline instead of border?** `outline` never affects layout (no added width or height at any offset), and `outline-offset: -1px` draws the ring just inside the image edge so it hugs the corner radius instead of sitting outside it.

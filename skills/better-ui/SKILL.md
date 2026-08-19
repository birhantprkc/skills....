---
name: better-ui
description: Design engineering principles for making interfaces feel polished. Use when building UI components, reviewing frontend code, implementing animations, hover states, shadows, borders, micro-interactions, enter/exit animations, choosing or reviewing icons, or any visual detail work. Triggers on UI polish, design details, "make it feel better", "feels off", stagger animations, theme switch transitions, border radius, optical alignment, image outlines, box shadows, icons, icon stroke weight, icon states, motion restraint.
---

# UI polish

Polish comes from a pile of small details that compound. This skill is the reference for which details are worth having and what values they take.

When reviewing, slow the interface down. Replay motion at 10% speed in the browser's Animations panel, then walk every state: hover, focus, active, loading, empty. What feels off at 10% speed is what is subtly wrong at full speed.

Keep the project's component library, tokens, and density. Match its established motion language except where a principle below prescribes an exact interaction.

Text wrapping, font rendering, tabular numbers, and text spacing belong to `better-typography`. Hit areas, focus, keyboard support, ARIA, and reduced motion belong to `better-accessibility`. Grouping, section spacing, breakpoints, and spatial RTL belong to `better-layout`.

## Quick reference

| Category | When to Use |
| --- | --- |
| [Surfaces](surfaces.md) | Border radius, optical alignment, shadows, image outlines |
| [Animations](animations.md) | Interruptible transitions, scale on press, skipping animation on page load, theme switching, motion restraint |
| [Enter & Exit](enter-exit.md) | Staged entrances, stagger timing, exit transitions |
| [Icon Transitions](icon-transitions.md) | Cross-fading an icon on state change, with and without a motion library |
| [Icons](icons.md) | Icon stroke weight, states via `currentColor`, outline vs fill, sizing, RTL flipping |
| [Performance](performance.md) | Transition specificity, `will-change` usage |
| [Review Output Format](review-output.md) | Severity scale, findings table, verification, verdict |

## Core principles

### Concentric border radius

Outer radius = inner radius + padding. Mismatched radii on nested elements is the most common thing that makes interfaces feel off.

### Optical over geometric alignment

When geometric centering looks off, align optically. Buttons with icons, play triangles, and asymmetric icons all need manual adjustment.

### Shadows for elevation, borders for structure

For buttons, cards, and containers whose border exists only to create depth, prefer layered transparent `box-shadow` values. Keep borders that communicate structure or state: dividers, layout separators, and selected or focus states.

### Interruptible animations

Use CSS transitions for interactive state changes: they can be interrupted mid-animation. Reserve keyframes for staged sequences that run once.

### Split and stagger enter animations

For an infrequent staged entrance where sequence helps communicate hierarchy, break content into semantic chunks and stagger them by ~100ms instead of animating one container. Do not stagger routine, high-frequency interactions. [Stagger and exit recipes](enter-exit.md).

### Subtle exit animations

Use a small fixed `translateY` instead of full height. Exits should be softer than enters. Use `ease-out` for both enter and exit transitions.

### Contextual icon animations

Animate icons with `opacity`, `scale`, and `blur` instead of toggling visibility. Use exactly these values: scale from `0.25` to `1`, opacity from `0` to `1`, blur from `4px` to `0px`. If the project has `motion` or `framer-motion` in `package.json`, match that package's import path (or the established nearby imports when both exist) and use `transition: { type: "spring", duration: 0.3, bounce: 0 }`; bounce must always be `0`. If no motion library is installed, keep both icons in the DOM (one absolute-positioned) and cross-fade with CSS transitions using `cubic-bezier(0.2, 0, 0, 1)`; this gives both enter and exit animations without any dependency. [Both recipes](icon-transitions.md).

### Image outlines

Add a subtle `1px` outline with low opacity to images for consistent depth. The color must be pure black in light mode (`oklch(0 0 0 / 0.1)`) and pure white in dark mode (`oklch(1 0 0 / 0.1)`), never a near-black like slate, zinc, or any tinted neutral. A tinted outline picks up the surface color underneath it and reads as dirt on the image edge.

### Scale on press

A subtle `scale(0.96)` on click gives buttons tactile feedback. Always use `0.96`; anything below `0.95` feels exaggerated. Add a `static` prop to disable it when motion would be distracting. [Recipes for CSS, Tailwind, and Motion](animations.md#scale-on-press).

### Skip animation on page load

Use `initial={false}` on `AnimatePresence` to prevent enter animations on first render. Verify it doesn't break intentional entrance animations.

### Suppress transitions on theme switch

A theme flip changes color, background, border, and shadow on nearly every element at once, so every transition on those properties fires together and the switch smears instead of snapping. Inject `*,*::before,*::after{transition:none !important}`, force a reflow, then remove it on the next frame. [Recipe](animations.md#suppress-transitions-on-theme-switch).

### Transition only what changes

Always specify exact properties: `transition-property: scale, opacity`. Tailwind's `transition-transform` covers `transform, translate, scale, rotate`.

### Use `will-change` sparingly

Only for `transform`, `opacity`, `filter`, the properties the GPU can composite. Never use `will-change: all`. Only add when you notice first-frame stutter.

### Match icon stroke to text weight

An icon next to text carries the text's optical weight: `1.5px` stroke beside regular (400) text, `2px` beside semibold (600). One stroke weight per icon set; never mix libraries on one surface.

### One SVG, recolored per state

Icons use `currentColor` and get their states (hover, selected, disabled) from CSS color and opacity, never from separate assets. Outline variant is the default; fill variant marks the active state.

### Motion restraint

No custom animation on high-frequency interactions: the attention cost repeats on every trigger. Motion is never the only feedback channel; every animated state change also needs a static cue (color, icon, label).

## Common mistakes

| Mistake | Fix |
| --- | --- |
| Same border radius on closely nested parent and child | Calculate `outerRadius = innerRadius + padding` |
| Icons look off-center | Adjust optically with padding or fix SVG directly |
| Border used only to fake elevation | Use layered `box-shadow` with transparency; keep structural and state borders |
| Jarring staged entrance or contextual exit | Stagger infrequent entrances and keep context-preserving exits subtle |
| Stateful icon or toggle animates its default state on page load | Add `initial={false}` to that `AnimatePresence`; preserve intentional page entrances |
| Theme toggle crossfades the whole page | Disable transitions for the swap, force a reflow, restore on the next frame |
| `transition: all` on elements | Specify exact properties |
| First-frame animation stutter | Add `will-change: transform` (sparingly) |
| Hairline icon beside bold text | Match the stroke width to the text weight |
| Separate icon assets per state | One `currentColor` SVG, states via CSS |
| Filled icons everywhere | Outline as default, fill only for the active state |
| Entrance animation on every hover or keystroke | Instant feedback or ≤150ms opacity/color transition |

## Reporting

A standalone UI-polish review is finished when every confirmed finding is reported in the format in [review-output.md](review-output.md), with verification and a verdict. Under `better-interface`, its format governs instead.

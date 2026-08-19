---
name: better-ui
description: Design engineering principles for making interfaces feel polished. Use when building UI components, implementing animations or hover states, or doing any visual detail work. Triggers on UI polish, "feels off", stagger animations, enter animations, theme switch transitions, border radius, optical alignment, image outlines, box shadows, icons, icon stroke weight, motion restraint.
---

# UI polish

Polish comes from a pile of small details that compound. This skill is the reference for which details are worth having and what values they take.

When reviewing, slow the interface down. What feels off at 10% speed is what is subtly wrong at full speed.

Keep the project's component library, tokens, and density. Match its established motion language except where a rule below prescribes an exact interaction.

Every duration, curve, scale, and blur below is a specific value, not a range to approximate. `cubic-bezier(0.2, 0, 0, 1)` is not `cubic-bezier(0.4, 0, 0.2, 1)`, and `0.96` is not `0.95`. Use what is written.

Text wrapping, font rendering, tabular numbers, and text spacing belong to `better-typography`. Hit areas, focus, keyboard support, ARIA, and reduced motion belong to `better-accessibility`. Grouping, section spacing, breakpoints, and spatial RTL belong to `better-layout`.

## Concentric border radius

Outer radius = inner radius + padding. Mismatched radii on nested elements is the single most common thing that makes an interface feel off. Radius, shadow, and outline recipes are in [surfaces.md](surfaces.md).

## Optical over geometric alignment

When geometric centering looks off, align optically. Buttons with icons, play triangles, and asymmetric icons all need a manual nudge.

## Shadows for elevation, borders for structure

For buttons, cards, and containers whose border exists only to create depth, prefer layered transparent `box-shadow` values. Keep the borders that communicate structure or state: dividers, layout separators, and selected or focus states.

## Interruptible animations

Use CSS transitions for interactive state changes, because they can be interrupted mid-animation. Reserve keyframes for staged sequences that run once.

## Split and stagger enter animations

For an infrequent staged entrance where sequence helps communicate hierarchy, break the content into semantic chunks and stagger them by ~100ms. Animating one container gets you less for the same cost. Leave routine, high-frequency interactions unstaggered. See [enter-exit.md](enter-exit.md).

## Subtle exit animations

Use a small fixed `translateY` rather than full height. Exits should be softer than enters. Use `ease-out` for both directions.

## Contextual icon animations

Animate icons with `opacity`, `scale`, and `blur` rather than toggling visibility. Use exactly these values: scale `0.25` to `1`, opacity `0` to `1`, blur `4px` to `0px`.

With a motion library (`motion` or `framer-motion` in `package.json`), match that package's import path, or the established nearby imports where both exist. Use `transition: { type: "spring", duration: 0.3, bounce: 0 }`. Bounce is always `0`.

Without one, keep both icons in the DOM with one absolutely positioned, and cross-fade with CSS transitions using `cubic-bezier(0.2, 0, 0, 1)`. That gives you enter and exit with no dependency. Both recipes are in [icon-transitions.md](icon-transitions.md).

## Image outlines

Add a subtle `1px` outline at low opacity to images for consistent depth. The color is pure black in light mode (`oklch(0 0 0 / 0.1)`) and pure white in dark mode (`oklch(1 0 0 / 0.1)`). Never a near-black like slate or zinc, and never a tinted neutral. A tinted outline picks up the surface color underneath it and reads as dirt on the image edge.

## Scale on press

A `scale(0.96)` on click gives a button tactile feedback. Always use `0.96`; anything below `0.95` feels exaggerated. Add a `static` prop to switch it off where motion would distract. See [recipes for CSS, Tailwind, and Motion](animations.md#scale-on-press).

## Skip animation on page load

Use `initial={false}` on `AnimatePresence` to keep enter animations off the first render. Check that it leaves intentional page entrances intact.

## Suppress transitions on theme switch

A theme flip changes color, background, border, and shadow on nearly every element at once. Every transition on those properties fires together, and the switch smears instead of snapping. Inject `*,*::before,*::after{transition:none !important}`, force a reflow, then remove it on the next frame. See the [recipe](animations.md#suppress-transitions-on-theme-switch).

## Transition only what changes

Always name the exact properties: `transition-property: scale, opacity`. Tailwind's `transition-transform` covers `transform, translate, scale, rotate`.

## Use `will-change` sparingly

Only for `transform`, `opacity`, and `filter`, the properties the GPU can composite. Never `will-change: all`. Add it when you notice first-frame stutter, and not before. See [performance.md](performance.md).

## Match icon stroke to text weight

An icon next to text carries the text's optical weight: `1.5px` stroke beside regular (400) text, `2px` beside semibold (600). One stroke weight per icon set, and one icon library per surface. Sizing and RTL flipping are in [icons.md](icons.md).

## One SVG, recolored per state

Icons use `currentColor` and take their states (hover, selected, disabled) from CSS color and opacity, never from separate assets. Outline is the default variant; fill marks the active state.

## Motion restraint

Give high-frequency interactions instant feedback or a transition of `150ms` or less on opacity and color. A custom animation there charges its attention cost on every single trigger.

Every animated state change also needs a static cue: color, an icon, or a label. Motion is never the only feedback channel.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Icons look off-center | Adjust optically with padding or fix SVG directly |
| Jarring staged entrance or contextual exit | Stagger infrequent entrances and keep context-preserving exits subtle |
| Theme toggle crossfades the whole page | Disable transitions for the swap, force a reflow, restore on the next frame |
| `transition: all` on elements | Specify exact properties |
| First-frame animation stutter | Add `will-change: transform` (sparingly) |
| Hairline icon beside bold text | Match the stroke width to the text weight |

## Reporting

**Severity.** `HIGH` breaks an interaction or makes motion unusable. `MEDIUM` is a visible inconsistency in surfaces, icons, or motion. `LOW` is isolated polish.

**Verification.** Without a browser: every state the component defines, meaning hover, focus, active, loading and empty, plus motion durations and easings read from the code. With one: walk each state, and replay motion at 10% speed in the browser's Animations panel. Report every check you could not run as `Not verified`.

**Format.** Group findings under the principle each violates, ordered by severity within a group:

| Severity | Location | Before | After | Why |
| --- | --- | --- | --- | --- |

`Location` cites `path/to/file:line`. `Before` and `After` share one row: the current implementation, then an actionable replacement. `Why` names the principle and the user impact. One row per root cause, listing every location it appears in.

End with a verdict. `Block` when any `HIGH` remains, `Needs changes` when only `MEDIUM` or `LOW` do, `Approve` when nothing actionable remains and you verified the coverage you claimed. With nothing to report, state "No actionable UI-polish findings", report verification, and end with `Approve`.

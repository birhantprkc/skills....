---
name: better-colors
description: Color systems for digital products. Use when creating or extending a palette, theming light and dark appearances, or auditing the colors in a codebase. Triggers on color palette, palette generation, color ramp, brand color, accent color, gray palette, status colors, color tokens, token naming, theming, dark mode colors, contrast ratio, APCA, gamut, display p3, oklch, color conversion, gradients, color meaning, increased contrast.
---

# Colors

A color system is a small set of ramps, named by role, applied consistently, and verified against the backgrounds they actually render on. Most color bugs are system bugs: a value picked in isolation, a token borrowed because it looked right, a pair nobody measured.

Never report a contrast value you did not measure, and never estimate a color you could compute. Colors are one of the few interface concerns with an exact answer, so produce the exact answer.

Contrast requirements belong to `better-accessibility`. Surfaces, shadows, and icon color belong to `better-ui`.

## Match the project's color system

Reuse the project's existing tokens and notation. A second color representation added to fix one value makes the palette harder to reason about, not easier: a consistent hex system beats a hex system with `oklch()` scattered through it.

For a genuinely new system, `oklch()` is the best default, because its numbers behave the way the ramp rules below describe. Everywhere else, a color library produces the same ramp in whatever notation the project already writes ([color-formats.md](color-formats.md)).

## A system is ramps, not colors

One neutral ramp, one accent ramp, and only the status ramps the product actually renders. A `warning` ramp nothing imports is maintenance for zero pixels. A second accent hue earns its place only when two things must be distinguishable at a glance.

## Every step has a job

A ramp is not a gradient to pick from by eye. Each step exists because a role needs it: page background, component hover, border, solid fill, body text. A step no role consumes should not be generated. Both the Tailwind `50`–`950` and Radix `1`–`12` conventions map to those roles ([palette-structure.md](palette-structure.md)).

## Name primitives by hue, semantics by role

Primitives name a value (`--blue-500`) and are never applied in a component. Semantic tokens name a job (`--color-text-secondary`), point at a primitive, and are the only tier components reference.

That seam is what makes theming possible. Without it, dark mode means auditing every usage to work out which ones meant "the accent" and which just wanted blue ([token-naming.md](token-naming.md)).

## Use a token only in its role

Never borrow a token because its value is right today. A separator used as a text color works until borders get lighter, and then the text goes with them. If a role has no token, add the token.

## Hold the hue across the ramp

Four properties define a well-formed ramp:

- Steps step evenly in *perceived* lightness, not in whatever the format calls lightness.
- Hue stays constant end to end.
- Vividness peaks mid-ramp and falls off at both ends.
- Steps sit denser at the light end than at the dark end.

Both ends stop short of pure black and white, which cannot carry hue at all. Use a color library rather than eyeballing it ([palette-generation.md](palette-generation.md)).

## One color, one meaning

Use a color for one purpose across the whole interface. Treat anything within `15°` of hue as the same color. If the accent means interactive, that hue on static text tells users to click something that is not clickable, and an interactive element rendered neutral misleads just as badly. Color is never the only carrier of meaning; `better-accessibility` owns that requirement.

## Fill exactly one action per view

When filled color encodes primary emphasis, one primary action gets it and peer actions stay neutral. Put the color on the background rather than the label: a filled button reads as primary across the room, while accent-colored text on a neutral button reads as a link.

Several colored backgrounds are fine when they encode distinct states or categories rather than competing as peers.

## Measure the rendered pair, then report

Measure a foreground against the background it actually renders on, not the page background. When a pair fails, report the pair, its measured value, and the threshold it misses, then leave the colors alone. A project's colors are a design decision. Change them only when asked, and remeasure after ([contrast.md](contrast.md)).

## Pick a gradient's interpolation space

The space is a look, not a correctness setting.

- **`in oklab`** is the best default: even brightness, no hue surprises.
- **`in oklch`** travels around the hue wheel instead of through the middle, so it stays vivid and sweeps through every hue between the stops. Reach for it when a two-hue gradient goes gray in the middle.
- **The sRGB default** darkens and mutes the midpoint. It is the look most interfaces already have, because it is what you get without asking.

See [color-usage.md](color-usage.md).

## Before you finish

| Mistake | Fix |
| --- | --- |
| A raw value where the project has a token | Reuse or add the correct role token in the project's existing notation |
| An isolated `oklch()` value dropped into a hex codebase | Preserve the established notation unless a color-system migration is in scope |
| A primitive like `--blue-500` used directly in a component | Point a semantic token at it and use that |
| Token named for its appearance (`--color-blue-button`) or first use (`--color-sidebar-gray`) | Name it for its role: `--color-accent-solid`, `--color-bg-surface` |
| `--color-primary` meaning the brand and `--color-text-primary` meaning body text | Reserve `accent` for the brand; let `primary` mean "most prominent of its group" |
| Semantic token used outside its role (separator as text) | Add a token for the missing role; never borrow by value |
| Ramp built by varying HSL lightness | Rebuild against perceived lightness with a constant hue |
| Ramp spaced evenly across the full range | Tighten the light end; `50` and `100` must be distinguishable as two surfaces |
| Same saturation number reused across hues | Match the same proportion of each hue's own maximum, not the raw value |
| Status hue that collides with the accent hue | Move it until destructive and primary actions are distinguishable side by side |
| Dark mode made by mechanically reversing the light palette | Reverse as a starting point, then reduce vividness, widen the dark end, and recheck every pair |
| `prefers-color-scheme` setting some tokens and a `.dark` class setting others | Pick one switching mechanism and use it throughout |
| Contrast fixed by changing hue | Change lightness, the channel contrast responds to |
| P3 color with no sRGB fallback | Declare the sRGB value first, then override inside `@media (color-gamut: p3)` |
| Palette verified only in light mode | Recheck every foreground/background pair in both appearances |

## Reporting

A standalone color review is finished when every confirmed finding is reported with verification and a verdict. The table structure and the verdict ladder are `better-interface`'s, in its `review-format.md`; the two things below are the ones specific to color.

**Severity.** `HIGH` makes content unreadable or assigns a misleading semantic color. `MEDIUM` is a noticeable theme, token, or gamut failure. `LOW` is isolated polish.

**Verification.** Contrast measured against the rendered background rather than the page background, gamut checks, and both light and dark appearances. A failing pair is reported, not repainted.

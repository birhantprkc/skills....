# Reading a screenshot

The method for a screenshot, where the answer is a reconstruction rather than a reading. Which property lands in which bucket:

| Exact from pixels | Ratios only | Unavailable |
| --- | --- | --- |
| Color values | Type sizes | Tokens and their names |
| Contrast between any two sampled colors | Spacing values | The stack and styling system |
| Relative proportion of anything measurable | Radii | Breakpoints |
| Which colors repeat, and where | Stroke and border weights | Motion, easing, duration |
| | | Every state but the captured one |

You do not know the capture's scale. A screenshot may be at 1×, 2×, or browser zoom, so a measured 30px could be 15pt of type or 30. Never report a `px` value for size or spacing from an image alone.

The single exception: where the image contains text you can identify as body copy, assume it is `16px` and express everything as a multiple of it. Say you did that, since the assumption may be wrong.

## Colors, which are the reliable part

Sample the actual pixels rather than describing what you see. Then hand the values to `better-colors`:

- Convert each to OKLCH, so lightness is comparable across hues.
- Sort by lightness to see whether the samples form a ramp, and whether steps sit denser at the light end.
- Check hue constancy across the ramp. A drifting hue means someone picked the ramp by eye.
- Measure contrast on every foreground and background pair you can isolate. This is exact and it is the most valuable single number an image can give you.
- Watch for a tinted neutral. A gray carrying a few percent of the accent hue is a deliberate choice worth naming.

## Type, by category not by name

You cannot identify a typeface from a screenshot with confidence, so do not claim one. You can read its category and its features, which is what actually transfers:

- **Category:** geometric sans, grotesque, humanist, transitional serif, slab. Say which and why.
- **Tells worth naming:** single or double-storey `a` and `g`, terminal angle, aperture, x-height against cap height. Also whether the digits are lining or old-style, and whether the figures look tabular.
- **Scale:** count the distinct sizes. Express them as multiples of the body size and derive the ratio.
- **Weight contrast:** how many weights, and how far apart. Two weights three steps apart reads deliberate; four weights one step apart reads accidental.
- **Measure:** count characters on a full line of body copy and compare against `better-typography`'s 60 to 75 range.

Where you want the actual face, say the category and suggest identifying it from the live page instead.

## Spacing, as a rhythm

Measure in the image, then divide everything by the smallest repeated gap. That quotient set is the rhythm, and it survives not knowing the scale.

Then check the ratio `better-layout` owns: the gap between groups against the gap within a group. A ratio at or above 2 means space is carrying the grouping. Below that, look for what else is doing the work, usually a border or a background shape.

## Say what the image hid

Close by naming what a screenshot could not show, because that list is where the reader would otherwise assume you looked:

- Hover, focus, active, disabled, loading, empty, and error states.
- Whether anything animates, and how.
- Behavior at any other width.
- Whether it is keyboard reachable, and whether focus is visible.
- The other appearance, light or dark.

Where any of these matter to the reader's question, say that a live URL would answer it and this image cannot.

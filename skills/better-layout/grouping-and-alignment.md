# Grouping & Alignment

How spacing, shapes, and shared edges communicate which things belong together.

## Group with Space, Not Lines

Three tools create grouping, in order of preference:

1. **Negative space** — the default. Related items sit close; unrelated items sit far apart.
2. **Background shapes** — a card or filled container, when a group needs to read as one unit (a selectable row, a draggable card).
3. **Separator lines** — last resort, for dense data where space would cost too much (tables, long settings lists).

The structural rule: the gap between groups must be at least 2× the gap within a group. If items inside a group are `8px` apart, groups need `16px`+ between them — otherwise the eye can't tell where one group ends.

```css
/* Good: spacing alone communicates the grouping */
.field-group { display: flex; flex-direction: column; gap: 8px; }
.form { display: flex; flex-direction: column; gap: 24px; }

/* Bad: uniform spacing plus lines to compensate */
.form > * { margin-bottom: 12px; border-bottom: 1px solid var(--separator); }
```

```html
<!-- Good: Tailwind -->
<div class="space-y-6">
  <div class="space-y-2">…field group…</div>
  <div class="space-y-2">…field group…</div>
</div>
```

When a separator is genuinely needed, keep it quiet: hairline width, low contrast, and never combined with a large gap (the gap already did the job).

## Keep Controls Distinct from Content

Interactive elements need a visual signal that they're interactive — a background, a border, an underline, or placement in a consistent control zone (toolbar, footer row). A control styled identically to static text is invisible.

```html
<!-- Bad: action looks exactly like the description text next to it -->
<p class="text-zinc-600">Your trial ends soon. Upgrade now</p>

<!-- Good: the action reads as an action -->
<p class="text-zinc-600">Your trial ends soon.</p>
<button class="font-medium text-blue-600">Upgrade now</button>
```

The inverse also holds: don't give static elements control styling. A non-clickable badge shaped exactly like the buttons beside it collects dead clicks.

## Align to Shared Edges

Alignment is what makes a layout scannable: the eye tracks straight edges to move through content. Pick a small set of alignment edges and put everything on them.

- Every stray edge — an icon 2px off the text edge, a card padded differently from its neighbor — reads as noise even when nobody can name the problem.
- Use one consistent indentation step (`16px`) to express hierarchy; deeper nesting repeats the same step.
- Numbers in tables right-align to the trailing edge (see `better-typography` for tabular figures); text left-aligns to the leading edge.

```css
/* Good: one shared leading edge, one indent step */
.section { padding-inline: 24px; }
.section .child { margin-inline-start: 16px; }

/* Bad: three unrelated leading edges in one column */
.header { padding-inline-start: 20px; }
.list-item { padding-inline-start: 14px; }
.footer { padding-inline-start: 24px; }
```

## Logical Properties, Not Physical

Always express horizontal position as leading/trailing, not left/right, so the layout mirrors automatically under `dir="rtl"`:

| Physical (avoid) | Logical (use) |
| --- | --- |
| `margin-left` | `margin-inline-start` |
| `padding-right` | `padding-inline-end` |
| `left: 0` | `inset-inline-start: 0` |
| `text-align: left` | `text-align: start` |
| `border-right` | `border-inline-end` |

```html
<!-- Good: Tailwind logical utilities -->
<div class="ms-4 pe-6 text-start">…</div>

<!-- Bad: breaks in RTL -->
<div class="ml-4 pr-6 text-left">…</div>
```

Reserve physical properties for things that genuinely refer to physical screen sides regardless of language — e.g. positioning relative to a device notch, or an element that must match a physical gesture direction.

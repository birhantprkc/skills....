# Hierarchy & Flow

Where content goes: reading order, importance placement, and how to hint at what's off-screen.

## Order by Importance

Readers scan top-to-bottom and leading-to-trailing. Place content accordingly:

- The most important information sits near the top and the leading edge; the further down and trailing something sits, the less attention it gets.
- Give essential information room. Don't bury the one number the user came for under rows of secondary detail — push secondary content into collapsed sections, tabs, or detail views.
- Within a row, the identifying content (name, title) leads; metadata and actions trail.

```html
<!-- Good: primary fact first, detail demoted -->
<div>
  <p class="text-2xl font-semibold">$4,320.00</p>
  <p class="text-sm text-zinc-500">Available balance</p>
</div>

<!-- Bad: the key fact is buried below the fold of the card -->
<div>
  <p class="text-sm">Account 4402 · Opened 2019 · Standard tier</p>
  <p class="text-sm">Last statement: June 30</p>
  <p class="text-sm">Balance: $4,320.00</p>
</div>
```

Think in **leading/trailing**, not left/right: combined with logical properties (see [grouping-and-alignment.md](grouping-and-alignment.md)), the same hierarchy mirrors correctly in RTL locales.

## Progressive Disclosure Needs an Affordance

Hiding complexity is good; hiding it without a cue is a trap. Every piece of off-screen or collapsed content needs a visible hint that it exists:

- **Peeking items.** In a horizontal scroller or carousel, size items so the next one peeks `16–32px` past the container edge. A row of cards that ends exactly at the edge looks complete, and nobody scrolls it.
- **Disclosure controls.** Collapsed sections get a chevron or "Show more" control; the label states what's hidden ("Show 12 more results"), not just "More".
- **Truncation cues.** Clamped text shows an ellipsis and a way to expand — see `better-typography` for truncation mechanics.

### The peeking-scroller recipe

Make the container's padding create the peek, and keep snap points aligned to the content edge:

```css
.scroller {
  display: flex;
  gap: 12px;
  overflow-x: auto;
  padding-inline: 24px;
  scroll-padding-inline: 24px;
  scroll-snap-type: x mandatory;
}
.scroller > * {
  flex: 0 0 calc(100% - 48px - 24px); /* container minus margins minus peek */
  scroll-snap-align: start;
}
```

```html
<!-- Tailwind -->
<div class="flex gap-3 overflow-x-auto px-6 [scroll-padding-inline:1.5rem] snap-x snap-mandatory">
  <div class="w-[80%] shrink-0 snap-start">…</div>
  <div class="w-[80%] shrink-0 snap-start">…</div>
</div>
```

The `80%` width is what creates the peek: the following card's leading `16–32px` stays visible at every snap position.

## Don't Overload the Entry Point

The first screenful is a table of contents, not the whole book. If everything is prominent, nothing is:

- One primary action per view (see `better-colors` for how color enforces this).
- Group secondary actions behind a menu once they exceed two or three.
- Prefer a short view that links deeper over a long view that shows everything at level one.

---
name: better-accessibility
description: Accessibility engineering for product interfaces. Use when building or reviewing UI components and custom widgets, or when the user reports a keyboard or screen-reader problem. Triggers on accessibility, a11y, WCAG, aria, focus ring, focus trap, keyboard navigation, tabindex, screen reader, sr-only, alt text, hit area, hover on touch, prefers-reduced-motion, autoplay, skip link, semantic HTML, form errors, disabled buttons, "not keyboard accessible".
---

# Accessibility

Most accessibility is free if you use the platform. Native elements ship with keyboard support, real labels announce themselves, and a visible focus ring is one CSS rule.

Reviewing means two walks. First keyboard-only: every flow completes without a mouse. Then screen-reader: every control announces a name, a role, and its state. When unsure, take the platform default over a custom rebuild, and remove ARIA rather than add it.

Write every fix in the styling system the project already uses.

Contrast measurement and color fixes belong to `better-colors`. Text sizing and iOS input zoom belong to `better-typography`. Spatial RTL layout belongs to `better-layout`.

## Quick reference

| Category | When to Use |
| --- | --- |
| [Focus & Keyboard](focus-and-keyboard.md) | Focus rings, skip links, tabindex, focus trapping, APG keyboard patterns |
| [Semantics & ARIA](semantics-and-aria.md) | Native elements first, button vs link, landmarks, accessible names, disabled states |
| [Forms](forms.md) | Labels, autocomplete, error messaging, input types |
| [Screen Readers](screen-readers.md) | Visually hidden content, live regions, toasts, alt text, SVG |
| [Hit Areas](hit-areas.md) | Target sizes, expanding hit areas, collision rules |
| [Motion & Zoom](motion-and-zoom.md) | `prefers-reduced-motion`, autoplay and timed UI, 200% zoom, reflow, rem vs px |
| [Review Output Format](review-output.md) | Severity scale, findings table, verification, verdict |

## Core principles

### Native elements first

The first rule of ARIA: don't use ARIA when a native element exists. `<button>` for actions, `<a href>` for navigation (it must support Cmd/Ctrl/middle-click), never `<div onClick>`. No ARIA is better than bad ARIA.

### Visible focus rings

Style `:focus-visible`, not bare `:focus`, so keyboard users get a ring and mouse users usually don't. Prefer the browser's unmodified focus indicator. If the design needs a custom ring, use a project focus token or another explicit color and verify the complete indicator against every adjacent color it crosses; `currentColor` is acceptable only after the same check. Use at least a `2px` solid perimeter or an equivalent visible area. Never use `outline: none` without a verified replacement, and preserve system colors in forced-colors mode.

### Full keyboard support

Every pointer interaction needs a keyboard path, following the ARIA APG patterns: Escape closes overlays, arrow keys move within composite widgets (tabs, menus, listboxes), Tab moves between widgets, Enter and Space activate. Only `tabindex="0"` (join the natural tab order) and `tabindex="-1"` (programmatic focus), never positive values, which break the natural order. Composite widgets use roving tabindex: the active item is `0`, all others `-1`.

### Trap and restore focus

Modals set `inert` on the background content, move focus inside on open, and return focus to the trigger on close. Add `overscroll-behavior: contain` so background content doesn't scroll.

### Minimum hit area

WCAG 2.5.8's Level AA baseline is a 24×24 CSS-pixel target or one of its defined spacing, equivalent-control, inline, user-agent, or essential exceptions. For easier activation, aim for 44×44px in touch contexts and 40×40px in desktop interfaces when density permits. Extend with a pseudo-element if the visible element should stay smaller. Never let extended hit areas overlap, and give decorative layers `pointer-events: none` so a glow or gradient never swallows the clicks meant for the control beneath it.

### Label and type every control

Every input gets a `<label for>` or wrapping `<label>`; a placeholder is never a label, and label and control share one hit target: no dead zones between a checkbox and its text. Add `autocomplete` with a meaningful `name`, and the correct `type` and `inputmode` for the keyboard. Never block paste; users paste passwords and one-time codes.

### Errors that announce

Keep submit enabled until the request starts, then disable with a spinner while keeping the original label. Validate on submit: mark failing fields with `aria-invalid="true"`, point `aria-describedby` at the inline error text, and focus the first invalid field. Use native `disabled` when a native control is genuinely unavailable. Use `aria-disabled="true"` only when retaining focusability or discoverability is intentional; then block pointer, keyboard, and form behavior in code and style the state explicitly.

### Accessible names everywhere

Icon-only buttons need a descriptive `aria-label`. Visible label text must appear in the accessible name. Decorative elements get `aria-hidden="true"`, never on a focusable element.

### Don't rely on color alone

Status needs a redundant cue: icon, text, or underline alongside the color. Determine which WCAG contrast requirement applies from the content and state, then use `better-colors` to measure the rendered foreground/background pair. When contrast fails, report the pair and requirement it misses; do not change the project's colors unless asked.

### Honor prefers-reduced-motion

Wrap motion in `@media (prefers-reduced-motion: no-preference)` so it is opt-in. Under reduced motion, replace slides and scales with opacity crossfades; kill parallax and autoplay entirely. Independent of the preference: autoplaying media needs a visible pause control, and toasts carrying actions or errors stay until dismissed.

### Announce dynamic content

Use `aria-describedby` for field-specific validation, a polite live region (`role="status"`) for non-urgent updates not tied to a control such as toasts or result counts, and `role="alert"` only for urgent errors not tied to a control. For reliable repeated polite announcements, render a stable empty region before updating its text; dynamically inserted alerts have different support and must be tested with the target screen readers.

### Alt text by purpose

Decorative images get `alt=""`, informative images describe the meaning, functional images describe the action: a search icon button is `alt="Search"`, not `alt="magnifying glass"`.

### Structure is navigation

Use headings that describe their sections and form a coherent outline; one page-level `<h1>` and properly nested levels are the recommended default, not standalone WCAG pass/fail rules. Expose one visible primary `<main>` landmark. When repeated navigation or chrome precedes it, make a "Skip to content" link the first focusable element. Anchored headings get `scroll-margin-top`.

### Survive zoom and text resize

The page must work at 200% zoom and reflow at 320px width without horizontal scrolling. Use `min-height` instead of fixed `height` on text containers, prefer `rem` breakpoints where they fit the codebase's conventions, and keep the viewport meta from capping how far the reader can zoom.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| `outline: none` to remove the focus ring | Style `:focus-visible` instead; mouse clicks won't show it |
| Custom focus color assumed to work everywhere | Verify the full indicator against every adjacent color and in forced-colors mode |
| `<div onClick>` for a button or link | `<button>` for actions, `<a href>` for navigation |
| Placeholder used as the only label | Add a visible `<label for>`; placeholders disappear on input |
| Positive `tabindex` to fix focus order | Fix the DOM order; only use `0` and `-1` |
| Repeated polite update inconsistently announced | Keep a stable empty status region and update its text; test the target screen readers |
| `assertive` live region for a routine toast | Use `polite`; reserve `assertive` for errors |
| `aria-hidden="true"` on a focusable element | Remove it or make the element non-focusable |
| Functional icon alt describes the picture | Describe the action: `alt="Search"`, not `alt="magnifying glass"` |
| Submit disabled until the form is valid | Keep it enabled; validate on submit and focus the first error |
| Decorative glow or gradient swallowing clicks | `pointer-events: none` on the layer, plus `aria-hidden="true"` |
| Hover treatment stuck after a tap on touch | Gate hover styling with `@media (hover: hover)` |
| Tooltip on a natively `disabled` control | Persistent text beside it, or `aria-disabled` so it stays focusable |

## Reporting

A standalone accessibility review is finished when every confirmed finding is reported in the format in [review-output.md](review-output.md), with verification and a verdict. Under `better-interface`, its format governs instead.

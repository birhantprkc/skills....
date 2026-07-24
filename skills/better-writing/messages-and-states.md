# Messages & States

Errors, empty states, and field hints — the copy that appears when something needs the user's attention.

## Errors

The best error is the one the design prevents: constrain the input, default sensibly, validate as the user goes. When a message is still needed:

- **Adjacent to the problem.** Inline at the failing field, not a summary toast at the top of the page.
- **An instruction, not a verdict.** Say what to do next:

| Bad | Good |
| --- | --- |
| That password is too short | Choose a password with at least 8 characters |
| Invalid name | Use only letters for your name |
| Oops! Something went wrong. | Unable to save. Check your connection and try again. |

- **No blame, no theater.** No "oops"/"uh-oh" (insincere), no exclamation marks, no scolding ("You entered the wrong…"). State the fix and stop.
- **Phrase positively.** "Use only letters", not "Don't use numbers or symbols".
- **Widespread error = design bug.** If many users hit the same message, the fix is redesigning the interaction, not polishing the wording.

Error markup — `aria-invalid`, `aria-describedby`, focusing the first invalid field — is covered by the `better-accessibility` skill.

## Empty States

An empty state is a first impression, not a dead end. It answers three things:

1. **What this place is** — one welcoming line, in the product voice.
2. **How to fill it** — one clear next action, as a button or link.
3. **Nothing load-bearing** — never put crucial persistent information in an empty state; it disappears the moment content exists.

```html
<!-- Bad: a shrug -->
<p>No results.</p>

<!-- Good: orientation plus a next step -->
<div class="text-center">
  <p class="font-medium">No projects yet</p>
  <p class="text-sm text-zinc-500">Projects keep your tasks and files together.</p>
  <button class="mt-4">Create a project</button>
</div>
```

Search and filter empty states name the query and offer an exit: "No results for 'quarterly'. Clear filters".

## Placeholders and Hints

- Placeholders show the expected format by example: `name@example.com`, `DD/MM/YYYY`.
- A placeholder is never the field's only label — it vanishes on input. Every field keeps a visible label; the markup rules live in the `better-accessibility` skill.
- Hints are instructions given before the mistake ("Use only letters"), not warnings after it, and they stay visible while the user types.

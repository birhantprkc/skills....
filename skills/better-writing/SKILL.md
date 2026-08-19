---
name: better-writing
description: >-
  UX writing and interface copy. Use when writing or reviewing any user-facing text.
  Triggers on UX writing, microcopy, button labels, link text, error messages, empty
  states, placeholder text, settings labels, capitalization, sentence case, voice and tone.
---

# Interface writing

Clear and brief beats clever, and consistent beats varied. The best error message is the interaction redesigned so the error cannot happen.

How copy renders (capitalization via `text-transform`, truncation, smart punctuation) belongs to `better-typography`. Error markup and announcements (`aria-invalid`, live regions) belong to `better-accessibility`. Room for translated strings belongs to `better-layout`.

## Recon the existing voice

Before writing or reviewing, read the interface copy nearby. Note the product's terminology, its localization conventions, and any voice or content style guide.

A deliberate brand voice is not a defect. Raise a difference from generic plain language only when it creates inconsistency, ambiguity, translation risk, or a tone the stakes don't support.

## One voice, flexible tone

The product has one voice, and its existing copy establishes it. A local edit does not get to invent a new one. Keep terms consistent: if it's "Archive" in the menu, it isn't "Move to storage" in the toast. Tone flexes with the stakes:

| Context | Tone |
| --- | --- |
| Success, onboarding, empty states | Warm, can be light |
| Routine actions, settings | Neutral, minimal |
| Errors, destructive confirmations | Calm, plain, zero playfulness |
| Data loss, security | Serious, explicit |

## Address the reader directly

In instructional copy, write "you", not "the user". In errors, "we" invites ambiguity and reads as deflection, so prefer "Unable to load content" over "We're having trouble loading this content". An established first-person brand voice can stay in low-stakes copy where it still reads clearly.

Use possessives sparingly: "Favorites" beats "Your Favorites". Hold one perspective throughout a flow.

## Plain words over clever ones

Choose words a tired reader understands on the first pass, and delete every word that does no work. No idioms, no colloquialisms, no humor that won't translate.

Skip unnecessary gender: "Subscribers can post recipes", not "each subscriber can post his or her recipes". Match the input device: "tap" on touch, "click" with a pointer, "select" when both are possible.

Never assemble a sentence from fragments around a variable (`"You have " + n + " new messages"`). Word order changes per language. Use a full templated string with proper pluralization.

## Verb-first buttons

A button label starts with a verb naming the specific action: "Send", "Save draft", "Delete project". Never "OK!", "Let's go!", or a bare "Yes" and "No" on a consequential action.

A confirmation button repeats the consequence, so the dialog is answerable without reading the body. "Delete this project?" offers `Delete project` and `Cancel`.

## Consistent flow vocabulary

A multi-step flow uses one vocabulary throughout: "Get started" to enter, "Continue" or "Next" (pick one) to advance, "Done" to finish. Alternating synonyms across steps makes users wonder whether the buttons do different things.

## Links describe their destination

Link text has to make sense out of context, because screen-reader users navigate by a list of the page's links. Write "Read the billing docs". "Click here" fails this and the device-verb rule at once.

A bare "Learn more" breaks down as soon as two appear on one page. Suffix each one: "Learn more about exports".

## One capitalization policy

Pick title case or sentence case per element type, then apply it to every instance of that type. Sentence case is the safer default: calmer, no per-word rules to remember, and it localizes cleanly. "Save Changes" beside "Discard changes" reads as sloppiness.

## Settings describe the ON state

Label a toggle for what happens when it is on. "Send read receipts" lets users infer the off state. Labeling the negative ("Don't send read receipts") turns the toggle into a double negative.

Link straight to a referenced setting rather than describing the path to it: a "Notification settings" link, not "Go to Settings > Notifications > Email".

## Errors say how to fix, next to where it broke

An error is an instruction, and it belongs beside the field that failed:

| Bad | Good |
| --- | --- |
| That password is too short | Choose a password with at least 8 characters |
| Invalid name | Use only letters for your name |
| Oops! Something went wrong. | Unable to save. Check your connection and try again. |

No blame, no "oops", no exclamation marks. Phrase hints positively ("Use only letters", not "Don't use numbers or symbols") and show them before the mistake rather than after. When the same error keeps firing for many users, redesign the interaction instead of rewording it.

## Empty states point forward

An empty state says what this place is, how to fill it, and offers one clear next action:

```html
<!-- Bad: a shrug -->
<p>No results.</p>

<!-- Good: orientation plus a next step -->
<p class="font-medium">No projects yet</p>
<p class="text-sm text-zinc-500">Projects keep your tasks and files together.</p>
<button class="mt-4">Create a project</button>
```

A search or filter empty state names the query and offers an exit: "No results for 'quarterly'. Clear filters". Never park crucial persistent information in an empty state, because it disappears the moment content exists.

## Placeholders are examples, not labels

A placeholder shows the expected format: `name@example.com`, `DD/MM/YYYY`. It is never the field's only label, since it vanishes on input. Every field keeps a visible label.

## Common mistakes

| Mistake | Fix |
| --- | --- |
| Local rewrite ignores the product's established terminology or voice | Inspect nearby copy and the style guide before proposing a change |
| "The user" in instructional interface copy | Address the reader directly as "you" |
| "We're having trouble…" obscures responsibility or recovery | Use a direct status and next step: "Unable to load content" |
| `OK` / `Yes` confirming a destructive dialog | Repeat the consequence: "Delete project" |
| "Continue" on step 2, "Next" on step 3 | One flow vocabulary throughout |
| "Click here" or bare "Learn more" link | Describe the destination: "Read the billing docs" |
| "Save Changes" beside "Discard changes" | One capitalization policy per element type |
| "Don't send read receipts" toggle | Label the ON state: "Send read receipts" |
| "Oops! Something went wrong." | Say what to do, next to the failing field |
| "No results." as the whole empty state | Orient and point forward with a next action |
| Placeholder doing the label's job | Visible label; placeholder shows the format |
| `"You have " + n + " messages"` | Full templated strings with pluralization |

## Reporting

A standalone writing review is finished when every confirmed finding is reported with verification and a verdict. Call the Skill tool with `better-interface` for the table structure and the verdict ladder; the two things below are the ones specific to writing.

**Severity.** `HIGH` misleads the user or hides how to recover from an error. `MEDIUM` breaks voice, terminology, or capitalization consistency. `LOW` is isolated wording polish.

**Verification.** Nearby copy read for voice and terminology, every label checked against the action it performs, and each error checked for a stated fix.

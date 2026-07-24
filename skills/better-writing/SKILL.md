---
name: better-writing
description: UX writing and interface copy, from voice and button labels to error messages and empty states. Use when writing or reviewing any user-facing text: button and link labels, form errors, placeholders, settings labels, onboarding flows, notifications, or empty states. Triggers on UX writing, microcopy, interface copy, product copy, copywriting, button labels, link text, error messages, empty states, placeholder text, settings labels, capitalization, title case, sentence case, voice and tone.
---

# Writing that disappears into the interface

Good interface copy is invisible: it answers the question the user already had and gets out of the way. Clear and brief beats clever, consistency beats variety, and the best error message is the interaction redesigned so the error can't happen. Apply these principles when writing or reviewing any user-facing text.

How copy renders — capitalization via `text-transform`, truncation, smart punctuation — is covered by the `better-typography` skill; error markup and announcements (`aria-invalid`, live regions) by the `better-accessibility` skill.

## Quick Reference

| Category | When to Use |
| --- | --- |
| [Voice & Language](voice-and-language.md) | Voice vs tone, addressing the reader, plain language, localizable phrasing |
| [Labels & Links](labels-and-links.md) | Button labels, flow vocabulary, link text, settings labels, capitalization |
| [Messages & States](messages-and-states.md) | Error messages, empty states, placeholders and hints |

## Core Principles

### 1. One Voice, Flexible Tone

The product has one voice — plain, direct, consistent terms for the same things. Tone flexes with context: a success toast can be light, a data-loss warning never is. Keep a shared term list so the same action never has two names.

### 2. Say "You", Never "The User"

Address the reader directly as "you". Avoid "we": it's ambiguous, and in errors it reads as deflection — "Unable to load content", not "We're having trouble loading this content". Use possessives sparingly ("Favorites" over "Your Favorites") and never switch perspective mid-app.

### 3. Plain Words Over Clever Ones

Choose easily understood words and delete every word that isn't needed. No idioms, colloquialisms, or humor that won't translate. Skip unnecessary gender: "Subscribers can post recipes", not "each subscriber can post his or her recipes". Match the input device: "tap" on touch, "click" with a pointer.

### 4. Verb-First Buttons

Button labels start with a verb naming the specific action: "Send", "Save draft", "Delete project" — never "OK!", "Let's go!", or bare "Yes"/"No" on consequential actions. Confirmation buttons repeat the consequence so the dialog is answerable without reading the body.

### 5. Consistent Flow Vocabulary

Multi-step flows use one vocabulary: "Get Started" to enter, "Continue" or "Next" (pick one) to advance, "Done" to finish. Don't alternate between synonyms across steps.

### 6. Links Describe Their Destination

Link text makes sense out of context — screen-reader users navigate by a list of the page's links. "Read the billing docs", never "Click here", and never a bare "Learn more" when several appear on one page.

### 7. One Capitalization Policy

Pick title case or sentence case per element type (all buttons, all headings) and apply it consistently; sentence case is the safer default. "Save Changes" beside "Discard changes" reads as sloppiness.

### 8. Errors Say How to Fix, Next to Where It Broke

An error is an instruction, adjacent to the failing field: "Choose a password with at least 8 characters", not "That password is too short". No blame, no "oops", no exclamation marks. If the same error keeps firing, redesign the interaction instead of rewording it.

### 9. Empty States Point Forward

An empty state says what this place is and how to fill it, with one clear next action ("Create your first project"). Never park crucial persistent information there — it disappears the moment content exists.

### 10. Placeholders Are Examples, Not Labels

Placeholders show the expected format (`name@example.com`, `DD/MM/YYYY`). A placeholder is never the field's only label; hints are stated positively before the user gets it wrong ("Use only letters"), not as prohibitions after.

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| "The user" in interface copy | Address the reader as "you" |
| "We're having trouble…" in an error | Drop the "we": "Unable to load content" |
| `OK` / `Yes` confirming a destructive dialog | Repeat the consequence: "Delete project" |
| "Continue" on step 2, "Next" on step 3 | One flow vocabulary throughout |
| "Click here" or bare "Learn more" link | Describe the destination: "Read the billing docs" |
| "Save Changes" beside "Discard changes" | One capitalization policy per element type |
| "Oops! Something went wrong." | Say what to do, next to the failing field |
| "Don't use numbers or symbols" hint | Phrase positively: "Use only letters" |
| "No results." as the whole empty state | Orient and point forward with a next action |
| Placeholder doing the label's job | Visible label; placeholder shows the format |

## Review Output Format

Always present changes as a markdown table with **Before** and **After** columns. Include every change you made, not just a subset. Never list findings as separate "Before:" / "After:" lines outside of a table. Group changes by principle using a heading above each table, and keep each row focused on a single diff so the reader can scan the whole list quickly.

### Example

#### Errors say how to fix
| Before | After |
| --- | --- |
| "Invalid password" | "Choose a password with at least 8 characters" |
| "We couldn't process your request" toast | Inline "Unable to save. Check your connection and try again." |

#### Verb-first buttons
| Before | After |
| --- | --- |
| "OK" on the delete confirmation | "Delete project" |
| "Let's go!" | "Create account" |

Rows should cite the specific file or component when it isn't obvious from the snippet. If a principle was reviewed but nothing needed to change, omit that table entirely: empty tables add noise.

## Review Checklist

- [ ] Same action has the same name everywhere; tone fits each context
- [ ] Reader addressed as "you"; no "the user", no "we" in errors
- [ ] No idioms, jargon, or unnecessary gender; phrasing survives translation
- [ ] Buttons start with a verb naming the action
- [ ] One flow vocabulary: Get Started / Continue / Done
- [ ] Links describe their destination and work out of context
- [ ] One capitalization policy per element type
- [ ] Errors are adjacent, actionable, and blame-free
- [ ] Empty states orient and offer one next action
- [ ] Every field has a visible label; placeholders only show format

## Reference Files

- [voice-and-language.md](voice-and-language.md): Voice vs tone, addressing the reader, plain language, localizable phrasing
- [labels-and-links.md](labels-and-links.md): Button labels, flow vocabulary, link text, settings labels, capitalization
- [messages-and-states.md](messages-and-states.md): Error messages, empty states, placeholders and hints

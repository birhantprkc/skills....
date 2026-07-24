# Voice & Language

The words themselves: one voice, direct address, and phrasing that survives translation.

## Voice vs Tone

Voice is who the product sounds like; tone is how it sounds right now. Voice stays constant — the same vocabulary, the same directness — while tone flexes with the stakes:

| Context | Tone |
| --- | --- |
| Success, onboarding, empty states | Warm, can be light |
| Routine actions, settings | Neutral, minimal |
| Errors, destructive confirmations | Calm, plain, zero playfulness |
| Data loss, security | Serious, explicit |

Keep a shared term list. The same action never has two names: if it's "Archive" in the menu, it isn't "Move to storage" in the toast. When naming a new concept, check what the rest of the product already calls adjacent things.

## Addressing the Reader

- **"You" and "your"** address the person using the product. Never "the user" — that's someone else's word for them.
- **Avoid "we".** It's ambiguous (the company? the app? the server?) and in errors it reads as deflection:

| Bad | Good |
| --- | --- |
| We're having trouble loading this content | Unable to load content |
| We couldn't verify your password | Password verification failed. Try again. |

- **Possessives sparingly.** "Favorites" usually beats "Your Favorites". If you adopt a perspective, keep it: don't mix "My files" and "Your files" in one product.

## Plain Language

- Choose words a newcomer understands; define specialized terms on first use if they're unavoidable.
- Delete every word that isn't needed. Read the sentence aloud; if you'd never say it, rewrite it.
- **No idioms, colloquialisms, or humor.** They're culture-specific, translate badly, and exclude anyone who doesn't share the reference — even harmless ones exclude non-native speakers.
- **Skip unnecessary gender.** Rewrite around gendered pronouns with plural or neutral phrasing:

| Bad | Good |
| --- | --- |
| Each subscriber can post his or her recipes | Subscribers can post recipes |
| The admin should check her dashboard | Admins should check the dashboard |

- **Match the input device.** "Tap" on touch, "click" with a pointer, "select" when either is possible.

## Writing for Localization

Phrasing choices decide how well copy translates:

- Short, complete sentences translate better than clever fragments.
- Never build sentences by concatenating fragments around variables ("You have " + n + " new messages") — word order changes per language; use full templated strings with proper pluralization.
- Layout must tolerate translated strings running 30–40% longer — that constraint lives in the `better-layout` skill.

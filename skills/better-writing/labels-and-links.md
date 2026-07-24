# Labels & Links

Buttons, flows, settings, links, and the capitalization that ties them together.

## Buttons

Button labels start with a verb naming the specific action:

| Bad | Good |
| --- | --- |
| `OK` / `Yes` on a destructive dialog | `Delete project` |
| `Let's do it!` | `Create account` |
| `Submit` on everything | `Send message`, `Save changes`, `Place order` |

- Confirmation buttons repeat the consequence, so the dialog is answerable without reading the body text. A dialog asking "Delete this project?" offers `Delete project` and `Cancel`, not `Yes` and `No`.
- Prioritize clarity over personality. Brand voice lives in onboarding and empty states, not in the label someone scans before a destructive click.

## Flow Vocabulary

Multi-step flows use one consistent vocabulary:

| Position | Label |
| --- | --- |
| Entry point | `Get Started` |
| Advancing | `Continue` or `Next` — pick one for the whole product |
| Finishing | `Done` |

Alternating synonyms across steps ("Continue", then "Next", then "Proceed") makes users wonder if the buttons do different things.

## Settings

- Label a toggle for what happens when it's ON: "Send read receipts" — users infer the off state. Never label the negative ("Don't send read receipts"), which turns the toggle into a double negative.
- Link directly to a referenced setting instead of describing the path to it: a "Notification settings" link, not "Go to Settings > Notifications > Email".

## Links

Link text describes the destination and survives out of context — screen-reader users navigate by a list of the page's links, and "Click here, click here, learn more" is unnavigable:

```html
<!-- Bad -->
<a href="/docs/billing">Click here</a> to learn more.

<!-- Good -->
<a href="/docs/billing">Read the billing docs</a>
```

- Never "Click here" — it also fails the device-verb rule on touch screens.
- A bare "Learn more" is acceptable only once per view; with several, suffix each: "Learn more about exports".

## Capitalization

Pick one style per element type and never mix:

| Style | Example | Notes |
| --- | --- | --- |
| Sentence case | "Save changes" | The safer default: calmer, no per-word case rules, localizes cleanly |
| Title case | "Save Changes" | Reads more formal; if chosen, every element of that type uses it |

The policy is per element type — all buttons one way, all headings one way — and the failure mode is mixing: "Save Changes" beside "Discard changes". Store copy in natural case and let CSS apply any uppercase presentation; that rule and `text-transform` live in the `better-typography` skill.

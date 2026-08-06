---
name: interface-review
description: >-
  User-invoked interface review of a change rather than a screen: uncommitted work, the current branch, or a pull request. Resolves the change scope, expands it to the surfaces it affects, reads both sides of the diff, and classifies every finding as introduced, a regression, or pre-existing, then hands the review to better-interface for domain routing, severity, and the verdict. Covers interface quality, not correctness, tests, or security. Supports quick and full review modes. Triggers on interface-review, review my branch, review my PR, review the diff, review my changes, review before pushing, design regression check, changed files interface review.
---

# Review the change, not just the code it left behind

A diff is not a surface. The lines a change deletes matter as much as the lines it adds, and the file it touches is rarely the whole of what it affects. Resolve what actually changed, expand it to the surfaces users will meet, then review that.

This skill owns change scope only: resolving the target from version control, expanding changed files to affected surfaces, reading both sides of the diff, and classifying each finding as introduced, a regression, or pre-existing. Domain rules belong to the six `better-*` skills. Routing, severity, consolidation, coverage, and the verdict belong to `better-interface`, which this skill hands the review to. Never duplicate or override their rules here.

This is an interface review. Correctness, tests, security, and performance belong to the project's general code review; say so and move on rather than reporting them here.

## Quick Reference

| Category | When to Use |
| --- | --- |
| [Scope Resolution](scope-resolution.md) | Default branch, merge-base, PR and fork targets, detached HEAD, shallow clones, renames, excluded paths |

## Core Principles

### 1. Resolve the Change Scope First

Parse the invocation as `[quick|full] [target]`. The first token is a mode only when it is exactly `quick` or `full`; anything else is the target. Mode defaults to `full`.

| Invocation | Resolves to |
| --- | --- |
| `/interface-review` | `full`, auto-detected |
| `/interface-review quick` | `quick`, auto-detected |
| `/interface-review working` | `full`, uncommitted changes only |
| `/interface-review pr 482` | `full`, pull request 482 |
| `/interface-review quick pr 482` | `quick`, pull request 482 |
| `/interface-review v2.1.0..HEAD` | `full`, explicit range |

When no target is supplied, resolve it in this order and stop at the first match:

1. `HEAD` is ahead of `git merge-base origin/<default-branch> HEAD` — scope is that range **plus** any uncommitted changes, with the commit count and the uncommitted file count stated separately.
2. The working tree is dirty — scope is the uncommitted changes.
3. Otherwise — scope is `HEAD~1..HEAD`, stated explicitly as a fallback.

Order matters. Checking the working tree first makes one stray formatting edit shadow a twelve-commit branch, and the report still claims full coverage.

Exclude lockfiles, snapshots, generated output, vendored code, and binaries from the scope, and name what was excluded. If the change scope is empty after exclusions, say so and stop.

### 2. A Diff Is Not a Surface

A changed file is evidence, not the review subject. Expand each changed file to the surfaces it renders in, and review the resulting interface rather than the hunk in isolation.

Expand one hop by default: the direct importers and callers of every changed module. Expand a second hop only for design tokens, theme values, and shared primitives, where a single line reaches the whole product. Review at most the five highest-traffic consumers and name the consumers you did not expand. An unbounded sweep produces coverage claims you cannot support.

### 3. Read the Removed Lines

Regressions are invisible in the post-change state. Read the `-` side of every hunk and search it for the signals below.

A signal is a lead, not a finding. Removing one of these is only a regression when nothing in the change replaces it, and this skill does not own that judgement — the domain skill does. Route each unmatched removal to its owner and report it only once that skill confirms the interface actually got worse. Equivalent replacements are common and clear the signal: `aria-label` giving way to `aria-labelledby`, an explicit `role` dropped because the element became a native `<button>`, `outline` replaced by a `box-shadow` focus ring that still meets the focus-indicator rule, a color literal replaced by a token that measures the same.

| Removed from the `-` side | What to check with the owning skill |
| --- | --- |
| `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-live`, `role=` | The control or region lost its accessible name, description, or announcement |
| `alt=`, `<label`, `for=`, `scope=` | Image, field, or table cell lost its programmatic association |
| `<button>`, `<a>`, `<nav>`, `<main>`, `<ul>` replaced by `div` or `span` | Keyboard and assistive-technology behavior was traded for styling |
| `:focus-visible`, `:focus`, `outline`, `tabindex` | Keyboard users lost the focus indicator or the element left the tab order |
| `prefers-reduced-motion`, `prefers-contrast` | Motion or contrast now ignores the user's system preference |
| `lang=`, `dir=`, logical properties swapped for `left` / `right` | Language metadata or direction-aware layout was dropped |
| `text-wrap`, `line-clamp`, `overflow-wrap`, `tabular-nums`, `font-feature-settings` | Text rendering, wrapping, or numeral alignment silently changed |
| A color token swapped for a literal, or a token swapped for a lighter one | The rendered contrast pair may now fail; measure it |

Once the owner confirms one, report it as a `Regression`, not as `Introduced`. The distinction tells the author they broke something that worked, which is different information from a new mistake.

### 4. Classify Every Finding

Give every finding one status:

- `Introduced` — the change created it.
- `Regression` — the change weakened something that was previously correct.
- `Pre-existing` — present in the touched code but not caused by this change.

`Introduced` and `Regression` findings are the review. `Pre-existing` findings are context: report them in their own section, cap them at three ordered by severity, and exclude them from the mode's finding cap. Without that cap, any change touching a legacy file turns into a full-file audit and the author cannot tell what their change is responsible for.

If a domain has no evidence anywhere in the change scope, mark it `Not reviewed — no evidence in the change scope`. That is a coverage statement, not a gap.

### 5. Hold the Change to Its Stated Intent

Read the pull request title and body, the linked issue, and the commit messages. They state what the change is meant to do; review whether the interface delivers it.

The finding this surfaces is the **incomplete** change, which a surface review cannot see because it inspects states "when present" and here the point is that they are absent:

- A new variant, size, or theme applied to some states but not all: hover, focus, active, disabled, loading, selected.
- A new user-facing string with no entry in the translation catalogue the project already maintains.
- A new component shipped without its empty, loading, error, disabled, or narrow-width state.
- A control added to one surface but not to the sibling surfaces that already carry its peers.

Do not report scope creep. Whether a change does too much is a process question, not an interface one.

### 6. Hand the Review to `better-interface`

Once the scope, the affected surfaces, and both sides of the diff are in hand, hand the review to `better-interface`. It routes to the six domain skills, applies the shared severity scale, consolidates systemic findings, enforces the mode's finding cap, and issues the verdict. Use its rules unchanged; this file adds only the `Status` column and the separate pre-existing section.

If `better-interface` is unavailable, report the resolved change scope and the file inventory, name `better-interface` as the missing skill, and stop. Do not invent a severity scale, a finding cap, or a verdict.

### 7. Never Mutate the Working Tree

A change review is read-only, including the checkout. Fetch pull request refs; do not check them out. `git fetch` writes to `.git` and is permitted. `gh pr checkout`, `git checkout`, `git switch`, and `git stash` rewrite the files the author has open — possibly failing against local edits, possibly discarding them — and are never permitted, in any mode.

Rendered verification is opt-in. Mark visual and runtime claims **Not verified** unless the project already exposes a cheap preview or the user asks for a rendered review. When the user does ask, add an isolated worktree at a throwaway path — `git worktree add /tmp/review-<n> refs/remotes/pr/<n>` — render from there, and remove it with `git worktree remove` when done. That leaves the author's working tree untouched, which is the point of the rule; a checkout does not, so it is not an alternative here.

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| One stray edit reviewed instead of the branch | Check `merge-base` before the working tree, and report both counts |
| Hunks reviewed without their consumers | Expand one hop, two for tokens and primitives, and name what you skipped |
| Only the `+` side of the diff read | Search the `-` side for removed accessibility, focus, motion, and text signals |
| An equivalent replacement reported as a regression | Route the removal to the owning skill and report only what it confirms |
| A removal reported as a new mistake | Status it `Regression` so the author knows it used to work |
| Every legacy issue in a touched file reported | Cap pre-existing findings at three in their own section |
| A pre-existing issue blocking the change | Keep pre-existing findings out of the finding cap and out of the change's verdict |
| Domain marked `Clear` when the diff never touched it | Mark it `Not reviewed — no evidence in the change scope` |
| A pull request checked out to review it | Fetch the ref and review it in place |
| Line numbers cited that do not exist on the reviewed ref | Cite against the head ref named in the scope block |
| Severity, caps, or the verdict restated here | Defer to `better-interface` |
| Correctness, test, or security findings in the report | Name the concern once, point at the project's code review, and drop it |

## Review Output Format

Use `better-interface`'s output format with the two additions below. Its severity scale, finding cap, consolidation rule, evidence rule, considered-but-rejected table, verification section, and verdict apply unchanged.

### Scope and Coverage

Open with the resolved change scope before the coverage table:

| Field | Value |
| --- | --- |
| Target | `branch`, `working`, `pr 482`, or the explicit range |
| Base ref | `origin/main` at `a1b2c3d` |
| Head ref | `refs/remotes/pr/482` at `e4f5g6h` |
| Commits | 7 committed, 2 files uncommitted |
| Files in scope | 12 after exclusions |
| Excluded | `pnpm-lock.yaml`, `src/__snapshots__/` — lockfile and snapshots |
| Surfaces expanded | `CheckoutPage`, `SettingsPanel`; not expanded: 3 further `Button` consumers |

Then the coverage table from `better-interface`, unchanged, covering all six domains.

### Findings

One table, ordered by severity then reach, covering `Introduced` and `Regression` findings only:

| # | Severity | Domain | Status | Location | Before | After | Why |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | HIGH | Accessibility | Regression | `src/Dialog.tsx:42` | `aria-label="Close"` removed in this change | Restore `aria-label="Close"` on the icon-only control | The close control had an accessible name before this change and no longer does |
| 2 | MEDIUM | Layout | Introduced | `src/Toolbar.tsx:18` | `margin-left: 16px` on the new action | `margin-inline-start: 16px` | The new physical property breaks the direction-aware layout the rest of the toolbar uses |

Respect the mode's finding cap. If there are no `Introduced` or `Regression` findings, omit the table and state "No actionable interface findings in this change."

### Pre-existing

Place after Considered but Rejected. At most three, highest severity first, excluded from the finding cap and from the verdict:

| Severity | Domain | Location | Issue |
| --- | --- | --- | --- |
| MEDIUM | Typography | `src/Toolbar.tsx:7` | Numeric badges use proportional figures; predates this change |

State plainly that these are not this change's responsibility. Omit the section when there are none.

### Verification and Verdict

Follow `better-interface`. List the exact git and `gh` commands run and their observed results, and mark every visual or runtime claim **Not verified** unless the interface was actually rendered. The verdict covers `Introduced` and `Regression` findings only; a change with nothing but pre-existing findings is an `Approve`.

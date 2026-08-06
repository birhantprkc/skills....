---
name: interface-review
description: >-
  User-invoked interface review of a change rather than a screen: uncommitted work, the current branch, or a pull request. Resolves the change scope, expands it to the surfaces it affects, reads both sides of the diff, and classifies every finding as introduced, a regression, or pre-existing, then hands the review to better-interface for domain routing, severity, and the verdict. Covers interface quality, not correctness, tests, or security. Supports quick and full review modes. Triggers on interface-review, review my branch, review my PR, review the diff, review my changes, review before pushing, design regression check, changed files interface review.
---

# Review the change, not just the code it left behind

A diff is not a surface. The lines a change deletes matter as much as the lines it adds, and the file it touches is rarely the whole of what it affects. Resolve what actually changed, expand it to the surfaces users will meet, then review that.

This skill owns change scope only: resolving the target from version control, expanding changed files to affected surfaces, reading both sides of the diff, and classifying each finding as introduced, a regression, or pre-existing. Domain rules belong to the six `better-*` skills. Mode, routing, severity, consolidation, coverage, the finding cap, the output format, and the verdict belong to `better-interface`, which this skill hands the review to. Never duplicate or override their rules here.

This is an interface review. Correctness, tests, security, and performance belong to the project's general code review; say so and move on rather than reporting them here.

## Quick Reference

| Category | When to Use |
| --- | --- |
| [Scope Resolution](scope-resolution.md) | Target vocabulary and commands, default branch, merge-base, PR and fork targets, awkward repository states, renames, excluded paths, consumer expansion |
| [Removed Signals](removed-signals.md) | What to look for on the `-` side of a hunk and which skill owns each removal |

## Core Principles

### 1. Resolve the Change Scope First

`better-interface` owns mode parsing: the leading token is a mode only when it is exactly `quick` or `full`, and mode defaults to `full`. Everything after it is the target, so `/interface-review quick pr 482` is a `quick` review of pull request 482 and `/interface-review` resolves its target automatically. [Scope Resolution](scope-resolution.md) holds the accepted targets and the exact command for each.

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

Regressions are invisible in the post-change state. Read the `-` side of every hunk and search it for the signals in [Removed Signals](removed-signals.md).

A signal is a lead, not a finding. Removing one of these is only a regression when nothing in the change replaces it, and this skill does not own that judgement — the domain skill does. Route each unmatched removal to its owner and report it only once that skill confirms the interface actually got worse.

Once the owner confirms one, report it as a `Regression`, not as `Introduced`. The distinction tells the author they broke something that worked, which is different information from a new mistake.

### 4. Classify Every Finding

Give every finding one status:

- `Introduced` — the change created it.
- `Regression` — the change weakened something that was previously correct.
- `Pre-existing` — present in the touched code but not caused by this change.

`Introduced` and `Regression` findings are the review; `Pre-existing` findings are context. Decide the status from the diff, not from the file: a line the change never touched is `Pre-existing` even when it sits three lines from a hunk. Confirm it against the base ref rather than by eye when it matters:

```bash
git blame -L <line>,<line> "$BASE" -- path/to/file
```

Hand every finding to `better-interface` with its status attached and let it apply its own cap and verdict rules to each.

### 5. Hold the Change to Its Stated Intent

Read the pull request title and body, the linked issue, and the commit messages. They state what the change is meant to do; review whether the interface delivers it.

The finding this surfaces is the **incomplete** change, which a surface review cannot see because it inspects states "when present" and here the point is that they are absent:

- A new variant, size, or theme applied to some states but not all: hover, focus, active, disabled, loading, selected.
- A new user-facing string with no entry in the translation catalogue the project already maintains.
- A new component shipped without its empty, loading, error, disabled, or narrow-width state.
- A control added to one surface but not to the sibling surfaces that already carry its peers.

Do not report scope creep. Whether a change does too much is a process question, not an interface one.

### 6. Hand the Review to `better-interface`

Once the scope, the affected surfaces, and both sides of the diff are in hand, hand the review to `better-interface` with the resolved scope block and a status on every finding. It routes to the six domain skills, applies the shared severity scale, consolidates systemic findings, enforces the cap, and issues the verdict — including how statuses affect the cap and the verdict, under its **Change-Scoped Reviews** section. Use its rules unchanged.

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
| A line near a hunk statused `Introduced` | Status by what the diff touched, confirmed with `git blame` against the base ref |
| A pull request checked out to review it | Fetch the ref and review it in place |
| Line numbers cited that do not exist on the reviewed ref | Cite against the head ref named in the scope block |
| Mode, severity, caps, the output format, or the verdict restated here | Defer to `better-interface` |
| Correctness, test, or security findings in the report | Name the concern once, point at the project's code review, and drop it |

## Review Output Format

`better-interface` owns the output format, including the four change-scoped additions under its **Change-Scoped Reviews** section: the scope block, the `Status` column, the `Pre-existing` section, and how statuses affect the cap and the verdict. Follow it as written and add nothing here.

This skill supplies two inputs to that format. Fill the scope block:

| Field | Value |
| --- | --- |
| Target | `branch`, `working`, `staged`, `pr 482`, or the explicit range as entered |
| Base ref | `origin/main` at `a1b2c3d` |
| Head ref | `refs/remotes/pr/482` at `e4f5g6h` |
| Commits | 7 committed, 2 files uncommitted |
| Files in scope | 12 after exclusions |
| Excluded | `pnpm-lock.yaml`, `src/__snapshots__/` — lockfile and snapshots |
| Surfaces expanded | `CheckoutPage`, `SettingsPanel`; not expanded: 3 further `Button` consumers |

And attach a status to every finding, per principle 4.

Under `better-interface`'s **Verification**, list the exact `git` and `gh` commands run and their observed results, including any write to `.git` — a fetch, a deepen, a `set-head`, a worktree — so the read-only claim in principle 7 is auditable.

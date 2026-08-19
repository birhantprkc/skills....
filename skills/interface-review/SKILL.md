---
name: interface-review
disable-model-invocation: true
description: >-
  Interface review of a change rather than a screen: uncommitted work, the current branch, or a pull request. Covers interface quality, not correctness, tests, or security.
---

# Change review

This skill reviews a change rather than a screen. It resolves the change scope, expands the changed files to the surfaces they affect, reads both sides of the diff, and classifies every finding.

Scope is all it owns. Domain rules belong to the `better-*` skills. Mode, severity, consolidation, coverage, the cap, the output format, and the verdict belong to `better-interface`, which this skill hands the review to.

Correctness, tests, security, and performance belong to the project's general code review. Name the concern once and move on.

## The change, not the codebase

The author is asking "did I make this worse?". So report what the change caused, and stay mostly quiet about what it merely touched. Three pre-existing findings is a courtesy. Thirty is a different review, and one nobody asked for.

Read the change before forming an opinion of it. The stated intent decides what counts as incomplete, and a diff you have only skimmed produces findings about code that the next hunk already fixed.

## Core principles

### 1. Resolve the change scope first

`better-interface` owns mode parsing. Everything after the mode is the target, so `/interface-review quick pr 482` is a `quick` review of pull request 482. [Scope Resolution](scope-resolution.md) holds the accepted targets and the command for each.

With no target supplied, resolve in this order and stop at the first match:

1. `HEAD` is ahead of `git merge-base origin/<default-branch> HEAD`: that range **plus** any uncommitted changes, with the commit count and the uncommitted file count stated separately.
2. The working tree is dirty: the uncommitted changes.
3. Neither: there is no change to review. Stop and ask, per **With no change, ask rather than invent one**.

Order matters. Check the working tree first and one stray formatting edit shadows a twelve-commit branch, while the report still claims full coverage.

Exclude lockfiles, snapshots, generated output, vendored code, and binaries, and name what you excluded. An empty scope after exclusions reaches the same place by a different route.

### 2. With no change, ask rather than invent one

A clean tree with nothing ahead of the merge base means the user asked to review a change that does not exist. Never fall back to `HEAD~1..HEAD` on your own. The last commit is whatever happened to land, often a merge, often someone else's work. A report on it is indistinguishable from a report on what the user meant.

State the repository facts you found, then offer the routes and wait. [Nothing to Review](scope-resolution.md#nothing-to-review) holds the commands:

- **The last commit**, `HEAD~1..HEAD`, named by short SHA and subject, so the user sees what they would get before choosing it.
- **A target they name**: `pr <n>`, a branch, a ref, or a range, resolved per **Resolve the change scope first**.
- **A whole-repository interface audit**, which is not a change review. Hand it to `better-interface` as a repository-scope review and drop this skill's scope block, statuses, and pre-existing section. With no change, every finding is pre-existing and the classification carries no information.

Check for an open pull request on the current branch before asking, and offer it first when one exists. A branch whose commits already landed on the base resolves to no change, while its pull request is still exactly what the user meant.

Where the scope emptied out after exclusions, say which files were excluded and ask the same way. Never report a review of nothing as `Approve`.

### 3. A diff is not a surface

A changed file is evidence, not the review subject. Its **blast radius** is the set of surfaces it renders in; review those.

Expand the blast radius one hop by default: the direct importers and callers. Expand a second hop only for design tokens, theme values, and shared primitives, where one line reaches the whole product.

Review at most five consumers, ordered by [the rule in Scope Resolution](scope-resolution.md#expanding-to-consumers). Then state how many you did not expand. A sweep with no bound cannot support the coverage it claims, and a cutoff left unstated reads as completeness.

### 4. Read the removed lines

Regressions are invisible in the post-change state. Read the `-` side of every hunk against [Removed Signals](removed-signals.md).

A signal is a lead, not a finding. A removal is only a regression when nothing in the change replaces it, and the domain skill owns that judgement. Route each unmatched removal to its owner and report only what that skill confirms. Then status it `Regression`, which tells the author they broke something that worked rather than made a new mistake.

### 5. Classify every finding

Give every finding one status:

- `Introduced`: the change created it.
- `Regression`: the change weakened something previously correct.
- `Pre-existing`: present in the touched code but not caused by this change.

Status by what the diff touched, not by which file it sits in: a line the change never touched is `Pre-existing` even three lines from a hunk. Confirm against the base ref when it matters:

```bash
git blame -L <line>,<line> "$BASE" -- path/to/file
```

Hand every finding up with its status attached and let `better-interface` apply its cap and verdict rules.

### 6. Hold the change to its stated intent

Read the pull request title and body, the linked issue, and the commit messages, then review whether the interface delivers what they claim.

This is what surfaces the **incomplete** change. A surface review cannot see it, because a surface review inspects the states that are present, and here the point is the ones that are absent:

- A new variant, size, or theme applied to some states but not all: hover, focus, active, disabled, loading, selected.
- A new user-facing string with no entry in the translation catalogue the project maintains.
- A new component with no empty, loading, error, disabled, or narrow-width state.
- A control added to one surface but not to the siblings that already carry its peers.

Do not report scope creep. Whether a change does too much is a process question, not an interface one.

### 7. Hand the review to `better-interface`

With the scope, the affected surfaces, and both sides of the diff in hand, hand the review to `better-interface` with the scope block and a status on every finding. It routes to the domain skills, applies severity, consolidates, enforces the cap, and issues the verdict. Its `review-format.md` holds the format, including the four change-scoped additions.

If `better-interface` is unavailable, report the resolved scope and the file inventory, name it as the missing skill, and stop. Do not invent a severity scale, a cap, or a verdict.

### 8. Never mutate the working tree

A change review is read-only, including the checkout. Fetch pull request refs; never check them out. `git fetch` writes only to `.git` and is permitted. `gh pr checkout`, `git checkout`, `git switch`, and `git stash` rewrite the files the author has open, failing against local edits or discarding them, and are never permitted in any mode.

Rendered verification is opt-in: mark visual and runtime claims **Not verified** unless the project exposes a cheap preview or the user asks for a rendered review. When they do, use an isolated worktree (`git worktree add /tmp/review-<n> refs/remotes/pr/<n>`) and remove it when done.

## Before you finish

| Mistake | Fix |
| --- | --- |
| One stray edit reviewed instead of the branch | Check `merge-base` before the working tree, and report both counts |
| The last commit reviewed because there was no change | State the repository facts and offer the last commit, a named target, or a repository audit |
| Hunks reviewed without their consumers | Expand one hop, two for tokens and primitives, and name what you skipped |
| Only the `+` side of the diff read | Search the `-` side for removed accessibility, focus, motion, and text signals |
| An equivalent replacement reported as a regression | Route the removal to the owning skill and report only what it confirms |
| A removal reported as a new mistake | Status it `Regression` so the author knows it used to work |
| A line near a hunk statused `Introduced` | Status by what the diff touched, confirmed with `git blame` against the base ref |
| A pull request checked out to review it | Fetch the ref and review it in place |
| Line numbers cited that do not exist on the reviewed ref | Cite against the head ref named in the scope block |
| Mode, severity, caps, the output format, or the verdict restated here | Defer to `better-interface` |
| Correctness, test, or security findings in the report | Name the concern once, point at the project's code review, and drop it |

## Review output format

`better-interface` owns the format, including the four change-scoped additions. Follow its `review-format.md` as written and add nothing here.

This skill supplies the scope block:

| Field | Value |
| --- | --- |
| Target | `branch`, `working`, `staged`, `pr 482`, or the range as entered |
| Base ref | `origin/main` at `a1b2c3d` |
| Head ref | `refs/remotes/pr/482` at `e4f5g6h` |
| Commits | 7 committed, 2 files uncommitted |
| Files in scope | 12 after exclusions |
| Excluded | `pnpm-lock.yaml`, `src/__snapshots__/`: lockfile and snapshots |
| Surfaces expanded | `CheckoutPage`, `SettingsPanel`; 3 further `Button` consumers not expanded |

Plus a status on every finding, per **Classify every finding**.

Under `better-interface`'s **Verification**, list the exact `git` and `gh` commands and their results. Include every write to `.git`: a fetch, a deepen, a `set-head`, a worktree. That is what makes the read-only claim in **Never mutate the working tree** auditable.

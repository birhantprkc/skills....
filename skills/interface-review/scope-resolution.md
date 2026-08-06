# Scope Resolution

Exact commands for turning a review target into a file list. Every command here is read-only against the working tree: `git fetch` writes to `.git`, everything else only reads. Never run `gh pr checkout`, `git checkout`, `git switch`, or `git stash` — resolving a scope never requires moving the author's files. An isolated `git worktree add` at a throwaway path is the one exception, and only for opt-in rendered verification as described in the skill.

## Default branch

Try in order, and stop at the first that answers:

```bash
git symbolic-ref --quiet --short refs/remotes/origin/HEAD   # -> origin/main
gh repo view --json defaultBranchRef -q .defaultBranchRef.name
git config --get init.defaultBranch
```

If `refs/remotes/origin/HEAD` is missing, ask the remote rather than guessing. This needs the network and writes the ref under `.git`, which leaves the working tree untouched and is permitted; note it in the Verification section:

```bash
git remote set-head origin --auto
```

If there is no remote at all, fall back to a local `main` or `master`, and state in the scope block which base you assumed.

## Merge base

```bash
BASE=$(git merge-base origin/main HEAD)
git rev-list --count "$BASE"..HEAD          # commits in the change
git diff --name-status "$BASE"...HEAD       # files, with rename detection
```

Use three dots (`"$BASE"...HEAD`) so the diff is against the merge base and not against whatever has landed on the base branch since. Two dots reports every upstream commit as part of the change.

If the base branch is stale, refresh the remote ref before computing the merge base:

```bash
git fetch origin main --no-tags
```

## Targets

These are the accepted targets. Anything else in the invocation after the mode is treated as a `<ref>`.

| Target | Commands |
| --- | --- |
| `working` | `git diff --name-status HEAD` plus `git ls-files --others --exclude-standard` for untracked files |
| `staged` | `git diff --name-status --cached` |
| `branch` | `git diff --name-status "$BASE"...HEAD`, where `BASE` is the merge base above |
| `branch` with uncommitted work | The `branch` diff, plus `git diff --name-status HEAD` **and** `git ls-files --others --exclude-standard`; report the two counts separately |
| `pr <n>` | Fetch, then diff — see below |
| `<ref>` | `git diff --name-status "$(git merge-base <ref> HEAD)"...HEAD` |
| `<a>..<b>` | `git diff --name-status <a>..<b>` — two dots as entered |
| `<a>...<b>` | `git diff --name-status <a>...<b>` — three dots as entered |

`git diff --name-status HEAD` reports tracked changes only. Any target that includes uncommitted work must pair it with `git ls-files --others --exclude-standard`, or a newly added component or stylesheet is silently dropped from a scope the report claims to cover in full.

When the user supplies an explicit range, use the dots they wrote. `<a>..<b>` compares the two endpoints; `<a>...<b>` compares `merge-base(<a>, <b>)` with `<b>`. Rewriting `release..feature` to three dots drops everything between `release` and the merge base, which is often exactly what the user asked to see. State the resolved range in the scope block so the choice is visible.

## Pull requests

Fetch the head into a remote-tracking ref and review it in place. This works for forks, which `origin/<branch>` does not:

```bash
gh pr view <n> --json title,body,headRefName,headRefOid,baseRefName
git fetch origin "pull/<n>/head:refs/remotes/pr/<n>" --no-tags
BASE=$(git merge-base origin/<baseRefName> "refs/remotes/pr/<n>")
git diff --name-status "$BASE"..."refs/remotes/pr/<n>"
```

Read files at that ref with `git show refs/remotes/pr/<n>:path/to/file`. Do not open the working-tree copy; on a fork PR it is a different file.

`gh pr diff <n>` is a fine shortcut for the patch text, but it gives no way to read unchanged context or expand to consumers, so fetch the ref as well.

**Citations.** `better-interface` requires `path/to/file:line`. Line numbers from a fetched ref do not necessarily match the working tree. Cite against the head ref, and declare that ref and its SHA in the scope block so the numbers are resolvable.

**Intent.** The `title` and `body` from `gh pr view` are the stated intent for principle 5. Add the commit subjects when the body is empty:

```bash
git log --format='%s%n%b' "$BASE".."refs/remotes/pr/<n>"
```

## Awkward repository states

| State | Detection | Handling |
| --- | --- | --- |
| Detached HEAD | `git symbolic-ref --quiet HEAD` fails | Use the merge base against the default branch; name the SHA, not a branch, in the scope block |
| Shallow clone | `git rev-parse --is-shallow-repository` is `true`, or `merge-base` returns nothing | `git fetch --deepen=50 origin` and retry; repeat once at `--deepen=200`, then report the scope as unresolvable |
| No remote | `git remote` is empty | Fall back to local `main`/`master`, or to `HEAD~1..HEAD`; state the assumption |
| No merge base | Unrelated histories | Review `HEAD~1..HEAD` and say the branch has no common ancestor with the base |
| Newly initialized repo | `git rev-parse HEAD` fails | Review the working tree and untracked files only |
| Mid-rebase or mid-merge | Any of `rebase-merge`, `rebase-apply`, `MERGE_HEAD` exists — see below | Stop and say the tree is mid-operation; a diff taken now is not the change |
| Submodule pointer moved | Diff shows a `-Subproject commit` line | Report the pointer move; do not descend into the submodule |

Resolve those in-progress paths through git rather than assuming `.git/` is a directory. Inside a linked worktree — the one this skill recommends for rendered verification — `.git` is a file and the real per-worktree state lives elsewhere, so a literal `.git/MERGE_HEAD` test silently reports a clean tree:

```bash
for state in rebase-merge rebase-apply MERGE_HEAD CHERRY_PICK_HEAD; do
  test -e "$(git rev-parse --git-path $state)" && echo "in progress: $state"
done
```

Deepening a shallow clone writes to `.git` but not to the working tree. It is permitted; note it in the Verification section.

## Renames

Rename detection is on by default for `--name-status`, which reports `R100 old/path new/path`. Raise the similarity window when a file was moved and edited in the same change:

```bash
git diff --find-renames=40% --find-copies-harder --name-status "$BASE"...HEAD
```

Review a rename as a move, not as a delete plus an add. Everything that survived the move is unchanged code, and only the genuine edits are in scope.

## Excluded paths

Exclude these from the change scope and name what you excluded in the scope block. They are machine-authored and carry no interface rules.

| Category | Patterns |
| --- | --- |
| Lockfiles | `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `bun.lock`, `bun.lockb`, `Cargo.lock`, `composer.lock`, `Gemfile.lock`, `poetry.lock`, `uv.lock` |
| Snapshots and fixtures | `__snapshots__/`, `*.snap`, `*.approved.*`, `test-results/`, `playwright-report/` |
| Generated output | `dist/`, `build/`, `out/`, `.next/`, `.turbo/`, `.svelte-kit/`, `coverage/`, `storybook-static/`, `*.min.js`, `*.min.css`, `*.map` |
| Generated sources | `*.gen.ts`, `*.generated.*`, `*.d.ts` emitted by a build, GraphQL and Prisma client output |
| Vendored code | `vendor/`, `third_party/`, `node_modules/` |
| Binaries and media | `*.png`, `*.jpg`, `*.webp`, `*.avif`, `*.woff2`, `*.mp4`, `*.pdf` |

Two exceptions worth keeping in scope: a **font file** added or swapped is a `better-typography` change, and an **image** added to a component is a `better-ui` and `better-accessibility` change through its `alt` text and outline. Review the code that references them, not the bytes.

Apply the exclusions as pathspecs so the file count in the scope block is the reviewed count:

```bash
git diff --name-only "$BASE"...HEAD -- . \
  ':(exclude,glob)**/*.lock' ':(exclude,glob)**/*-lock.json' \
  ':(exclude,glob)**/*lock.yaml' ':(exclude,glob)**/*.lockb' \
  ':(exclude,glob)**/dist/**' ':(exclude,glob)**/build/**' \
  ':(exclude,glob)**/__snapshots__/**'
```

Two traps make this silently under-exclude, and both leave the scope block claiming a count it did not deliver:

- **Match the real filenames, not the category.** `*.lock` catches `yarn.lock` and `Cargo.lock` but not `package-lock.json` or `pnpm-lock.yaml` — the two most common lockfiles in a JavaScript repo. Cover each suffix the table above actually lists.
- **Use `glob` magic for `**`.** Without it, `*` crosses `/` and `**/dist/**` requires a leading directory, so it excludes `packages/a/dist/` and misses a root-level `dist/`. With `:(exclude,glob)`, `**/` matches zero or more directories and both are caught, while `mydist/` is correctly kept.

Verify rather than assume: run the diff with and without the pathspecs and confirm the count dropped by exactly the files you named as excluded.

## Expanding to consumers

Principle 2 expands one hop, two for tokens and primitives. Find the hop with the project's own resolver where one exists, otherwise by import path.

`git grep` searches the working tree by default. Search the reviewed ref instead by passing it after the pattern, so the consumer set matches the change under review — on a pull request, the working tree is a different revision and will miss importers the change itself added:

```bash
REV=refs/remotes/pr/482          # or HEAD for a local branch
git grep -l "from ['\"].*<module-name>" "$REV" -- '*.ts' '*.tsx' '*.js' '*.jsx' '*.vue' '*.svelte'
git grep -ln "<ComponentName>" "$REV" -- '*.tsx' '*.vue' '*.svelte'
```

Results come back as `<rev>:path/to/file`. Read those files with `git show "$REV":path/to/file`, never the working-tree copy.

For a changed design token or theme value, search the token name rather than the file, since consumers reference the name and never import the file:

```bash
git grep -n -e '--color-accent' "$REV" -- '*.css' '*.tsx' 'tailwind.config.*'
```

Use `-e` whenever the pattern starts with a dash, or git parses the token name as an option.

Order the consumers by a rule you can actually evaluate, so the cutoff is reproducible instead of a guess:

1. **Route and layout entry points first.** The files a framework treats as a rendered surface, by its own convention — `app/**/page.*`, `app/**/layout.*`, `pages/**`, `routes/**`, `src/views/**`, `*.astro` pages. These are surfaces on their own; everything else only appears inside one.
2. **Then the remainder by importer count.** A component pulled in by twenty files carries more of the change than one pulled in by two. Count it:

   ```bash
   git grep -l "<ComponentName>" "$REV" -- '*.tsx' '*.vue' '*.svelte' | wc -l
   ```

3. **Break ties by proximity to the change** — the consumer in the same package or feature directory first, since it is likeliest to depend on what actually moved.

Review the first five, then state the count you did not expand and how they ranked. Do not claim a surface matters more than another on grounds you cannot show; if the ordering was arbitrary past a point, say that instead.

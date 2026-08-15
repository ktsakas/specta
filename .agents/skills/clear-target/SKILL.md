---
name: clear-target
description: >
  Make the working tree identical to origin/main. Discards every local
  change, untracked file, and ignored file. Use when the user runs
  /clear-target or asks to reset the folder to match GitHub exactly.
disable-model-invocation: true
---

# Clear target

Reset this repository so the working tree matches `origin/main` exactly. This is irreversible: uncommitted edits, untracked files, and gitignored files (including `.env` and `node_modules`) are deleted.

Do not run these commands unless the user invoked this skill.

## Steps

Run from the repository root, in this order. Stop on the first failure.

1. Confirm this directory is a git work tree (`git rev-parse --is-inside-work-tree`). If it is not, stop.
2. `git fetch origin`
3. If HEAD is not `main`, `git checkout --force -B main origin/main`. This moves onto `main` without leaving a feature branch pointed at `origin/main`.
4. `git reset --hard origin/main`
5. `git clean -fdx`

Do not skip `git clean -fdx`. `reset --hard` does not remove untracked or ignored files.

## Done when

- Current branch is `main`
- `git status` is a clean work tree, matching `origin/main`
- No leftover untracked or ignored files that are not in the remote tree

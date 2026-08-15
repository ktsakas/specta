---
name: spec-it
description: >
  Compare the running product to spec/ and update spec.md so a
  later compile would rebuild the current program. Report the
  high-level deltas first. Use when the user runs /spec-it, or
  asks to sync the spec, update spec.md from the code, or capture
  product changes into the specification.
---

# Spec-it

`spec/` is the reimplementation contract. Bring it in line with the current program so a new LLM running compile would rebuild what exists now.

Do not implement product features. Do not edit files outside `spec/` unless the user asks.

## Steps

1. Read every file under `spec/`. If `spec/` is missing or empty, stop and say so.
2. Inspect the current program at product level: user-visible behavior, layout, persistence, seed/data, metadata, stack, and stated constraints. Use git history only as a hint for recent intent. The comparison is **program vs spec**, not commit-by-commit.
3. List high-level deltas: things the spec would cause a compile to omit, contradict, or implement differently.
4. Drop anything a competent compile would already infer from the existing spec (styling polish, hover/focus treatment, internal hooks, helper names, bugfixes that restore specified behavior, file-by-file structure the spec does not already mandate).
5. Tell the user the deltas you will capture, in plain language. Then edit `spec.md` (and any other `spec/` files that are part of the contract) so those deltas are specified at the same altitude as the rest of the document.
6. Prefer amending the existing section over adding a new one. Remove or rewrite constraints that the program no longer follows. Keep names, labels, storage keys, and user-facing copy exact.

## What belongs in the spec

- New or removed user-facing behavior
- Changed product identity, chrome copy, or metadata
- Persistence, seed, or data-model changes a reimplementation must honor
- Layout or interaction rules a compile would get wrong if left unspecified
- Constraints that are now true or no longer true

## What does not belong

- Implementation choices (component names, CSS class names, debounce internals, plugin function names) unless the spec already names that layer
- Visual details already covered by the visual system
- Exhaustive edge-case lists the surrounding spec already implies

## Done when

- The user has been told what changed at a high level
- `spec/` would cause a fresh compile to include those changes
- Inferable detail was left out

---
name: compile
description: >
  Implement the program described in spec/. Read every file in that
  folder as the authoritative specification and build the product to
  match it. Use when the user runs /compile or asks to compile,
  implement, or build from the spec.
---

# Compile

`spec/` is the specification for the program to build. Implement it.

## Steps

1. List every file under `spec/` at the repository root.
2. Read all of them. Their contents are the complete, authoritative specification for the program.
3. If `spec/` is missing or empty, stop and say so.
4. Implement the specified program in this repository. Follow the spec as written: stack, structure, behavior, layout, and constraints. Do not add features the spec does not ask for. Do not skip requirements it does ask for. Do not edit files under `spec/`.
5. Keep going until the implementation matches the spec, including installing dependencies and leaving the project in a runnable state.
6. Audit the **running** product, not only source. Start it and exercise every specified behavior with real seed or default data, including empty and error paths.
   - A requirement is not met if it exists only in code, markup, or state. The user-facing result has to match the spec.
   - If output is missing or wrong despite correct-looking implementation, find the layer that dropped or overrode it and fix that.
7. Re-read the spec against what the running product actually does. Fix every gap before declaring done.

## Done when

- The working tree contains an implementation of what `spec/` describes
- Specified constraints (stack, layout, persistence, out-of-scope items) are respected
- The running product was exercised with real data, and specified behavior is present in the result, not only in source

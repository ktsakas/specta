---
name: compile-variant
description: >
  Read a variant under variants/ and apply it to spec/, then
  implement the updated spec by running the compile skill.
  Use when the user runs /compile-variant or asks to compile
  a variant.
---

# Compile variant

A variant is a markdown file under `variants/` (including subdirectories) that describes how to change `spec/` before implementing the product.

## Steps

1. Identify the variant. If the user named one, resolve it to a path under `variants/` (accept a stem such as `svelte`, a filename such as `svelte.md`, or a path under `variants/`). If they did not name one and exactly one markdown file exists under `variants/`, use it. If several exist, stop and ask which one.
2. Read the variant file. If it links to other markdown under `variants/`, read those too.
3. Read every file under `spec/`.
4. Edit files in `spec/` so they specify the product the variant describes. Change only what the variant requires. Do not implement the product in this step. Do not edit files outside `spec/` in this step.
5. Follow `.agents/skills/compile/SKILL.md` against the updated `spec/`.

## Done when

- `spec/` matches the variant
- The compile skill’s done criteria are met

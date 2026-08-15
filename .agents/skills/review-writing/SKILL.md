---
name: review-writing
description: >
  Review spec/ against a writing rule in writing-rules/.
  Use when the user runs /review-writing, names a writing
  rule (inconsistencies, extraneous, gaps), or asks to
  review the spec for contradictions, removable text, or
  underspecified gaps.
---

# Review writing

A writing rule is a markdown file under `writing-rules/` (including subdirectories) that says how to review `spec/`. Apply that rule. Do not implement the product.

## Steps

1. Identify the writing rule. If the user named one, resolve it to a path under `writing-rules/` (accept a stem such as `inconsistencies`, a filename such as `inconsistencies.md`, or a path under `writing-rules/`). If they did not name one and exactly one markdown file exists under `writing-rules/`, use it. If several exist, stop and ask which one.
2. Read the writing rule file. If it links to other markdown under `writing-rules/`, read those too.
3. Read every file under `spec/`. If `spec/` is missing or empty, stop and say so.
4. Review `spec/` using only that writing rule. Follow its “what to look for”, “what not to report”, and “how to report” sections. Do not apply other writing rules unless the user named them too.
5. Do not edit `spec/` or any other file unless the user asks after seeing the report.

## Done when

- The named writing rule was applied to all of `spec/`
- Findings follow that rule’s report format
- `spec/` is unchanged unless the user asked for edits

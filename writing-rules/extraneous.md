# Extraneous

Review every file under `spec/` for text that could be removed without changing what a later compile would build. This is a writing rule, not the product spec. Do not implement the product. Do not edit `spec/` unless the user asks.

## What to look for

Ask: if this sentence, list item, or paragraph were deleted, and the remaining `spec/` were given to a competent LLM, would the implementation differ in any user-visible or contract-level way?

Report passages that are:

- Restating a requirement already specified at the same altitude
- Inferable from surrounding rules (a compile would already do this)
- Implementation internals the spec does not need to pin down (component names, CSS class names, hook names, debounce wiring, file-by-file structure) unless another section already makes that layer part of the contract
- Exhaustive edge-case lists already implied by a general rule
- Visual polish already covered by the visual system
- Process narration, rationale, or history that does not constrain the product

## What not to report

- Names, labels, storage keys, endpoints, and user-facing copy (those must stay exact)
- Constraints a compile would get wrong if left unspecified
- Seed data, persistence keys, and empty/error paths that are part of the contract
- Cross-references that a reader needs to find the owning section

## How to report

For each finding, cite the file and section, quote or paraphrase the extra text, and say why a compile would not change if it were gone. Prefer larger removable units over nitpicks. If the spec is already tight, say so.

# Gaps

Review every file under `spec/` for missing or underspecified contract. This is a writing rule, not the product spec. Do not implement the product. Do not edit `spec/` unless the user asks.

## What to look for

Ask: if two competent LLMs compiled from this `spec/`, where would they reasonably invent different product behavior, layout, copy, data, or constraints because the spec is silent or ambiguous?

Report passages that are:

- Named surfaces (screens, views, buttons, endpoints, storage keys) with no behavior
- Behaviors that lack an empty, error, or first-load path the rest of the spec treats as required elsewhere
- Ambiguous words that admit two implementations (`should`, `may`, `or`, unspecified defaults)
- A list that looks complete but omits a case a nearby rule depends on
- Cross-references to files or sections that do not exist or do not cover the claim
- Constraints stated for one path (desktop, documents, local-dev) and left open for a sibling path that a compile must still build

## What not to report

- Details a competent compile would infer from an existing general rule
- Visual polish already owned by the visual system
- Implementation internals the spec intentionally leaves open
- Nice-to-haves outside the product the spec describes

## How to report

For each finding, cite the file and section, say what a compile would have to invent, and name the two plausible outcomes. Skip anything a later compile would not treat as part of the contract. If the spec is already decided everywhere it needs to be, say so.

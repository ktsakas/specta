# Inconsistencies

Review every file under `spec/` for contradictions. This is a writing rule, not the product spec. Do not implement the product. Do not edit `spec/` unless the user asks.

## What to look for

Read all `spec/` files together as one contract. Report a finding only when two or more statements cannot all be true, or when following one would cause a compile to violate another.

Look for:

- Requirements that conflict (behavior, layout, copy, stack, persistence, seed, or constraints)
- A later section that silently reverses an earlier one
- Cross-file clashes, especially `spec.md` vs `visual-system.md` vs any other `spec/` file
- Names, labels, keys, endpoints, or filenames that are defined differently in different places
- Inclusive lists that omit something another section requires, or exclusive lists that forbid something another section requires
- Defaults, empty states, and error paths that disagree
- Out-of-scope items that the rest of the spec still specifies

## What not to report

- Repetition that agrees with itself
- Detail one section leaves implicit and another spells out, if they are compatible
- Style or tone preferences that do not change the contract
- Implementation choices the spec does not pin down

## How to report

For each finding, cite the conflicting passages (file and section), quote the clash in one sentence, and say which interpretation a compile would have to pick. Group related clashes. If nothing contradicts, say so.

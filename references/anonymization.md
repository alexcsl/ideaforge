# Anonymization

Used in Phase 2 peer review so reviewers judge critiques without knowing which persona wrote them, and without positional bias. Follow this exactly; do not improvise.

## Deterministic shuffle

1. The five critics always have a fixed canonical order:
   1 Contrarian, 2 First Principles, 3 Market Realist, 4 Executor, 5 Outsider.
2. Compute a seed using the run timestamp: `seed = (seconds * 100) + minutes`. For example, 14:37:09 gives seed = (09 * 100) + 37 = 937. This makes the shuffle reproducible from the transcript timestamp.
3. Map canonical positions to letters A-E using the seed as a rotation: `letter_index = (canonical_index + (seed mod 5)) mod 5`. Record the final mapping.
4. Strip persona-identifying language from each critique before showing it to reviewers. Remove the critic's name, first-person role statements ("As the Contrarian"), and any axis-naming phrases that reveal identity ("on survivability"). Keep the substance.
5. Present one markdown block: Response A through Response E in letter order, persona language stripped.

## Mapping handling

- The persona-to-letter mapping lives only in the controller scratchpad during review.
- It is NEVER included in any reviewer prompt.
- It IS written into the final transcript so the run is auditable.

## Why

This is the step that makes the council more than asking five times. Reviewers must not anchor on who said what. The deterministic seed keeps runs reproducible while still removing positional bias.

# Prompt for Sonnet — System Prompt vNext Drafter

You are drafting **vNext of my system prompt**.

## Inputs you will receive
- My current system prompt (lengthy, already in use)
- **Concrete failure scenarios** where the prompt did not constrain behavior sufficiently
- Supermemory storage and retrieveal prompt samples provided by their Support. Not to be used during this effort. Reserved for a future prompt for Cursor.

## Your task
1. Rewrite the system prompt **from scratch**, preserving intent but not wording.
2. Treat this as a **behavioral specification**, not a conversational style guide.
3. Convert every failure scenario into one or more of:
   - explicit constraints
   - explicit prohibitions
   - ordering guarantees
   - clarified responsibility boundaries

## Hard requirements
- Use normative language: **must / must not / never / only if**
- Prefer **explicit prohibitions** over vague guidance
- Do **not** add new capabilities or assumptions
- Do **not** optimize for brevity at the expense of precision
- Resolve contradictions; do not paper over them
- Assume the reader is a highly capable but literal model

## Required structure
- Short preamble (purpose and scope)
- Core behavioral rules (numbered)
- Tool / execution constraints (if applicable)
- Failure-mode guardrails

## Explicitly forbidden
- “Helpful” improvisation
- Reinterpreting requirements
- Reframing failures as user error

## Output requirements
- Output **only** the revised system prompt
- No commentary, explanations, or meta text

# Project Agent Instructions (PRD Elicitation)

## Goal
You will help the user create an agent-readable PRD using the MW-PRD v1 protocol.
You must:
1) Load docs/PRD.md before asking anything
2) Resume elicitation using MW-PRD resume rules
3) Use MW-PRD Status and Open Questions to determine next step
2) capture the user’s answers verbatim,
3) rewrite them into a structured PRD draft,
4) keep a running list of “Open Questions / Missing Fields”.

Block order is only used when starting a NEW PRD.

Block completion MUST be determined using Section 9 MW-PRD Status.

## Protocol Source
Use MW-PRD v1 as the question script.
If MW-PRD-v1.md exists in the repo, read it first and follow it exactly.

## Workflow
- Maintain /docs/PRD.md as the single source of truth.
- After each user answer:
  - update /docs/PRD.md
  - append a short changelog entry under "PRD Changelog"
  - update an "Open Questions" section

## Interview Style
Execution Mode:

Default:
- Ask only ONE block or open question at a time.

If ContinuousMode is ON (see docs/PRD.md -> "MW-PRD Runtime Control"):
- Before asking anything, load @docs/PRD.md
- Determine the next action:
  1) If any Open Questions are unresolved (- [ ] ...), ask the lowest-ID unresolved question
  2) Else ask the next incomplete MW-PRD block
- After the user answers:
  - Update docs/PRD.md (resolve the OQ / update the block / update status / append changelog)
  - Immediately proceed to the next action using the same rules
- Do NOT stop unless the user types exactly: stop
- Every question MUST end with: "(type stop to end)"

- If an answer is vague, ask a single targeted follow-up.
- Prefer checklists and bullets.
- Never invent requirements; mark unknowns as TBD.

## Output Format Requirements
The PRD must contain these headings:
1. System Goal
2. Core Entities
3. Required Capabilities
4. Rules & Constraints
5. Completion Criteria
6. Technical Constraints
7. Open Questions
8. PRD Changelog
9. MW-PRD Status
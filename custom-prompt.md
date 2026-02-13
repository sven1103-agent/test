# MW-PRD Resume Execution

MANDATORY STATE LOAD:
@docs/PRD.md
@MW-PRD-v1.md

Rules:

1) If docs/PRD.md does NOT exist:
   - Initialize a new PRD
   - Start with Block 1

2) If docs/PRD.md exists:
   - Treat it as authoritative state
   - DO NOT restart Block 1 if Section 9 shows Block 1 is complete

3) Determine next action strictly in this order:
   a) If any Open Questions are unchecked (- [ ]), ask the lowest-ID unchecked question
   b) Else ask the next incomplete block according to Section 9 (MW-PRD Status)

4) Ask exactly ONE question now (either one Open Question or one Block prompt).

5) If ContinuousMode is ON in docs/PRD.md:
   - After each answer, update docs/PRD.md
   - Immediately repeat Step 3
   - Stop only if the user types: stop
   - Append "(type stop to end)" to each question

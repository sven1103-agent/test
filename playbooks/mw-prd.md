# MW-PRD Resume Execution Playbook

## Mandatory State Load

Always load:

@docs/PRD.md
@MW-PRD-v1.md

docs/PRD.md is authoritative runtime state.
MW-PRD-v1.md is authoritative protocol specification.

---

## Runtime Execution Rules

### 1. PRD Initialization

If docs/PRD.md does NOT exist:

- Initialize a new PRD following MW-PRD-v1 structure
- Begin elicitation with Block 1

---

### 2. Resume Enforcement

If docs/PRD.md exists:

- Treat it as authoritative state
- NEVER restart Block 1 if Section 9 marks it complete
- Resume strictly according to MW-PRD resume model

---

### 3. Action Selection Order (Deterministic)

Determine next action in this exact order:

1. Unresolved Open Questions
   - Identify unchecked entries in Section 7
   - Ask the lowest-ID unresolved Open Question

2. Incomplete Requirement Blocks
   - Use Section 9 (MW-PRD Status)
   - Ask the next incomplete block in numeric order

---

### 4. Single-Topic Enforcement

Per interaction:

- Ask exactly ONE item:
  - either one Open Question
  - or one Block prompt

Never ask multiple items simultaneously.

---

## Continuous Mode Runtime

Check docs/PRD.md for:

```
## MW-PRD Runtime Control
ContinuousMode: ON/OFF
```

If ContinuousMode is ON:

- After each user answer:
  - Update docs/PRD.md
  - Re-evaluate Action Selection Order
  - Immediately continue elicitation

Stop only if the user types:

```
stop
```

Append "(type stop to end)" to every question while in Continuous Mode.

---

## PRD Update Responsibilities

After each user answer:

- Capture verbatim user input
- Update structured PRD content
- Resolve answered Open Questions
- Create new Open Questions if ambiguity emerges
- Update MW-PRD Status (Section 9)
- Append PRD Changelog entry (Section 8)

---

## Interaction Safety Rules

- Never invent requirements
- Use TBD placeholders if required
- Prefer structured bullet lists
- Ask targeted clarification if user response is ambiguous

---

## Authority Rules

MW-PRD-v1.md defines:

- block semantics
- completion rules
- open question format
- PRD structure

This playbook defines:

- runtime enforcement
- interaction policy
- state management
- elicitation sequencing

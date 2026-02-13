# Minimal Working PRD Elicitation Protocol (MW-PRD v1.2 – Open-Questions-First)

## Purpose

MW-PRD defines a **minimal**, **iterative**, and **resumable** way to elicit requirements and maintain a PRD that is suitable for:

- Human readability
- Autonomous task decomposition
- Long-running agent execution
- Incremental refinement with deterministic resume behavior

---

## Core Principles

- **Minimality:** Only 6 requirement blocks are mandatory.
- **Iterativity:** Collect information one topic at a time.
- **Resumability:** Always resume where you left off.
- **Determinism:** PRD state must be machine-checkable.
- **Open-Questions-First:** Unresolved open questions are always handled before new blocks.

---

## Artifact Model

MW-PRD maintains one primary artifact:

- `docs/PRD.md`

The PRD MUST contain:

1. The 6 requirement blocks (sections 1–6)
2. Open Questions (section 7) in a structured format
3. PRD Changelog (section 8)
4. MW-PRD Status checklist (section 9)

> Optional: `docs/open-questions.md` MAY exist as a mirror of section 7, but `docs/PRD.md` is authoritative.

## PRD Discovery and Loading Rules (MANDATORY)

The agent MUST load the PRD using the following deterministic lookup order:

### Primary Location

docs/PRD.md

This file is the single authoritative source of requirement state.

### If docs/PRD.md Exists

The agent MUST:

1. Load the entire file
2. Use it as the only requirement state source
3. Ignore any PRD fragments elsewhere

### If docs/PRD.md Does NOT Exist

The agent MUST:

1. Create docs/PRD.md
2. Initialize it using MW-PRD structure
3. Begin elicitation at Block 1

### Multiple PRD Files Rule

If multiple PRD-like files exist:

- docs/PRD.md ALWAYS overrides others
- Agent MUST NOT merge PRDs automatically

### File Inclusion Requirement (Codex Specific)

The agent MUST explicitly include:

@docs/PRD.md

in reasoning context before asking any question.

---

## Resume & Execution Model (MANDATORY)

### Step 0 - Runtime Mode Detection

The agent MUST determine runtime mode before any interaction.
The agent MUST check `docs/PRD.md` for:

```md
## MW-PRD Runtime Control
ContinuousMode: ON/OFF
```

- If `ON` → Run Continuous Loop Mode
- If `OFF` or missing → Use Single-Topic Rule

The agent MUST display the selected mode and notify the user upon errors.

### Step 1 — Read State

When starting (or resuming) elicitation, the agent/facilitator MUST:

1. Read `docs/PRD.md`
2. Read **Section 7: Open Questions**
3. Read **Section 9: MW-PRD Status**

### Step 2 — Resume Priority (Open-Questions-First)

Elicitation MUST follow this order:

1. **Resolve open questions first** (lowest ID first)
2. If no open questions remain, proceed with the **next incomplete block** in numerical order
3. Only start at Block 1 if `docs/PRD.md` does not exist

### Step 3 — Single-Topic Rule

In each run/session:

- Ask **exactly one** open question *or* one MW-PRD block prompt (not both)
- Update `docs/PRD.md`
- Stop and wait for user input

This prevents looping and re-asking completed content.

### Step 4 — Continuous Loop Mode (MANDATORY when enabled)

If the facilitator/agent is running in an interactive session and is allowed to continue automatically, it MUST use **Continuous Loop Mode**.

#### Continuous Loop
Repeat until the user stops:

1. Read `docs/PRD.md`
2. If any open questions are unresolved:
   - Ask the **lowest-numbered unresolved** open question
3. Else:
   - Ask the **next incomplete** MW-PRD block prompt (in order)
4. After the user answers:
   - Update `docs/PRD.md` (resolve OQ / update block / update status / append changelog)

---

## Block Completion Rules (CRITICAL)

A MW-PRD block is considered **complete** ONLY if:

1. The block has structured content (not `TBD`), AND
2. There are **no unresolved open questions** associated with that block.

If either condition is false, the block MUST remain incomplete.

---

## Structured Open Questions Format (REQUIRED)

Open questions MUST be recorded in Section 7 using this exact format:

- Checkbox list item with an ID, block association, topic tag, and question text.
- Example:

```md
- [ ] OQ-B3-001 (Block=3 Required Capabilities) [Topic=Interface] What is the primary interface for MVP (CLI/Web UI/API/Library)?
```

### Required Fields

Each open question line MUST include:

- **ID:** `OQ-B{block}-{nnn}` (e.g., `OQ-B3-001`), or `OQ-B{block1}B{block2}-{nnn}` if shared.
- **Block association:** `(Block=...)` where block is 1–6 (or multiple).
- **Topic tag:** `[Topic=...]` a short label (e.g., Interface, Input, Output, Fetch, Redirects, Auth, RFC-Scope, Dependency).
- **Question text:** a single clear question.

### Ordering Rule

- Always resolve the **lowest-numbered unchecked** open question first.

### Resolution Rule

When an open question is answered:

1. Mark it as resolved: change `- [ ]` to `- [x]`
2. Append a short `Answer:` note on the same line OR immediately below it
3. Update the relevant PRD block section(s) (1–6) with the structured result
4. If the answer creates new ambiguity, add new open question(s) with new IDs

> IMPORTANT: Do not delete open questions. Keep them as `[x]` for traceability.

---

## MW-PRD Blocks (Questions + Required Output)

### Block 1 — System Goal
**Question:** What is the system supposed to accomplish, and who uses it?

**Required Output:**
- Primary users/actors
- Core value
- System boundaries (does / does not)

### Block 2 — Core Entities
**Question:** What are the main objects the system manages?

**Required Output (per entity):**
- Name
- Required fields
- Optional fields
- Relationships

### Block 3 — Required Capabilities
**Question:** What must users be able to do?

**Required Output:**
- Bullet list of user actions/capabilities

### Block 4 — Rules & Constraints
**Question:** What rules must the system enforce?

**Required Output:**
- Validation rules / invariants / scope rules

### Block 5 — Completion Criteria
**Question:** When is the system considered finished?

**Required Output:**
- Measurable completion conditions / stopping criteria

### Block 6 — Technical Constraints
**Question:** What technical/environment constraints apply?

**Required Output:**
- Tech stack constraints, pinned versions, hosting, security constraints

---

## Required PRD Structure

A PRD produced under MW-PRD MUST contain these sections in this order:

1. System Goal
2. Core Entities
3. Required Capabilities
4. Rules & Constraints
5. Completion Criteria
6. Technical Constraints
7. Open Questions
8. PRD Changelog
9. MW-PRD Status

---

## MW-PRD Status Checklist (REQUIRED)

The PRD MUST include:

```md
## 9. MW-PRD Status
- [ ] Block 1 – System Goal
- [ ] Block 2 – Core Entities
- [ ] Block 3 – Required Capabilities
- [ ] Block 4 – Rules & Constraints
- [ ] Block 5 – Completion Criteria
- [ ] Block 6 – Technical Constraints
```

### How to Update Status

- Mark a block as `[x]` ONLY if it meets the **Block Completion Rules** above.
- If any associated open question remains unchecked, the block MUST remain `[ ]`.

---

## Interaction Guidelines (for Agents/Facilitators)

- Ask **one** open question at a time using multiple-choice options when helpful.
- Convert answers into structured PRD statements.
- Never invent requirements; mark unknowns as `TBD` and create open questions.
- After each answer, update `docs/PRD.md` and append a changelog entry.

---

## Changelog Rules

Every update MUST append a line to Section 8:

```text
YYYY-MM-DD: Resolved OQ-Bx-yyy and updated Block x.
YYYY-MM-DD: Completed Block x (no remaining associated open questions).
```

---

## End of Protocol


# Codex Playbook Repository

This repository contains reusable **agent playbooks** for Codex-driven workflows.
Playbooks define structured, deterministic procedures that help agents and users
collaborate consistently across projects.

---

## Purpose

This repository provides:

- 📘 A **protocol specification** for requirements engineering (MW‑PRD)
- 🤖 A **runtime execution playbook** for Codex agents
- 🔄 A reusable workflow that supports resumable and deterministic requirement elicitation
- 🧩 A modular structure that allows additional playbooks to be added later

The goal is to separate:

| Layer | Purpose |
|--------|-------------|
| Protocol | Defines WHAT rules exist |
| Playbook | Defines HOW agents execute those rules |
| Orchestrator | Selects WHICH playbook runs |

---

## Available Playbooks

### MW‑PRD (Minimal Working PRD)

Purpose:
- Structured requirements elicitation
- Deterministic resume behavior
- Agent-readable PRD generation

Main artifacts:
- `docs/PRD.md` → Requirement state
- `MW-PRD-v1.md` → Protocol specification
- `playbooks/mw-prd.md` → Runtime execution playbook

---

## Repository Structure

```
.
├── AGENTS.md                 → Orchestrator / routing rules
├── MW-PRD-v1.md              → MW‑PRD protocol specification
├── playbooks/
│   └── mw-prd.md             → MW‑PRD runtime execution playbook
├── docs/
│   └── PRD.md                → Generated requirements state
├── codex-prompts/
│   └── mw-prd.md             → Codex shortcut prompt
```

---

## How To Use The MW‑PRD Playbook

### Step 1 — Install Codex Prompt Shortcut

This repository provides reusable slash-command prompts for Codex.

From the repository root run:

```
mkdir -p ~/.codex/prompts
ln -sf "$(pwd)/codex-prompts/mw-prd.md" ~/.codex/prompts/mw-prd.md
```

This creates the `/mw-prd` shortcut in Codex.

If your system does not support symlinks:

```
cp codex-prompts/mw-prd.md ~/.codex/prompts/mw-prd.md
```

---

### Step 2 — Start MW‑PRD

Inside Codex simply run:

```
/mw-prd
```

The agent will:

- Load the MW‑PRD playbook
- Load the PRD state
- Resume requirement elicitation
- Ask exactly one question at a time
- Respect Continuous Mode if enabled

---

## Continuous Mode

Continuous Mode can be enabled inside `docs/PRD.md`:

```
## MW-PRD Runtime Control
ContinuousMode: ON
```

When enabled, the agent will automatically continue asking questions
until the user types:

```
stop
```

---

## Design Philosophy

Playbooks are designed to be:

- Deterministic
- Resume-safe
- Human and agent readable
- Modular
- Extensible

---

## Adding Future Playbooks

New playbooks should follow the same structure:

```
playbooks/<playbook-name>.md
codex-prompts/<playbook-name>.md
```

Examples:

- Task decomposition
- Architecture design
- Test generation
- Code review automation

---

## Contribution Guidelines

When updating MW‑PRD:

- Update `MW-PRD-v1.md` for protocol changes
- Update `playbooks/mw-prd.md` for runtime behavior
- Keep `AGENTS.md` minimal (routing only)

---

## License

Add your preferred license here.

# Project Agent Orchestrator

This repository uses structured playbooks to guide agent workflows.

## Available Playbooks

### MW-PRD
- Purpose: Requirements elicitation
- Protocol file: playbooks/mw-prd.md
- Owns artifact: docs/PRD.md

## General Rules

- docs/PRD.md is authoritative for requirements
- Only one playbook should control execution at a time
- Playbooks must not modify artifacts owned by other playbooks

## Activation

/mw-prd
→ Load playbooks/mw-prd.md

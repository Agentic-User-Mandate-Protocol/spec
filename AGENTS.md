# Agent Instructions

This repository defines the Agentic User Mandate Protocol (AUMP). Treat it as a
protocol specification repo, not an SDK repo.

When editing:

- Keep normative requirements in `spec.md`.
- Keep rationale and tradeoffs in `design.md`.
- Keep machine-readable payload shape in `schemas/`.
- Keep transport-specific composition guidance in `bindings/`.
- Keep examples valid against the schemas whenever practical.
- Do not vendor MCP, A2A, UCP, or AP2 source into this repository.
- Use RFC 2119 language only when the statement is intended to be normative.

Core positioning:

- MCP: tool and context access.
- A2A: agent-to-agent task communication.
- UCP/AP2: commerce, checkout, payment, and transaction mandates.
- AUMP: user authority, preferences, negotiation, disclosure, escalation, and
  representation evidence.

# Conformance

AUMP v0.1 conformance has three levels.

## Level 1: Parser

An implementation is a Level 1 parser if it can:

- parse mandates as JSON;
- validate mandates against `schemas/mandate.schema.json`;
- reject inactive, expired, or malformed mandates.

## Level 2: Policy Evaluator

An implementation is a Level 2 policy evaluator if it can:

- perform all Level 1 checks;
- evaluate proposed actions against authority, budget, hard constraints,
  disclosure policy, and escalation policy;
- return `allowed`, `requires_escalation`, or `denied`;
- validate action evaluation payloads against
  `schemas/action-evaluation.schema.json`.

## Level 3: Runtime

An implementation is a Level 3 runtime if it can:

- perform all Level 2 checks;
- enforce revocation before material action;
- append evidence events for commitments and escalations;
- carry AUMP references through at least one binding: MCP, A2A, REST, UCP, or
  AP2;
- validate evidence events against `schemas/evidence-event.schema.json`;
- reject evidence events that leak private fields outside the mandate retention
  policy;
- provide a user-readable mandate summary before activation.

## Test Vectors

The `test-vectors/` directory contains initial examples. They are not a full
certification suite yet, but they define the expected shape for future
conformance tests.

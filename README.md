# Agentic User Mandate Protocol (AUMP)

AUMP is a portable user authority, preference, consent, and negotiation layer
for AI agents.

It fills the gap between agent infrastructure protocols and transaction
protocols:

- MCP lets agents use tools and context.
- A2A lets agents discover and communicate with other agents.
- UCP and AP2 let agents participate in commerce, checkout, and payment flows.
- AUMP tells an agent what it is allowed to want, reveal, concede, escalate,
  refuse, and prove on behalf of a user.

## Status

This repository contains the initial AUMP v0.1 draft. It is not final and should
be treated as a working specification for review, examples, and prototype
implementations.

## Core Idea

An AUMP mandate is a machine-readable representation contract between a
principal and an agent. It describes:

- who the agent represents;
- the purpose and scope of delegation;
- hard constraints and soft preferences;
- negotiation policy and concession limits;
- privacy and disclosure rules;
- escalation rules for asking the user;
- evidence needed to explain what happened later.

AUMP does not replace payment, checkout, identity, or agent messaging
protocols. It composes with them.

## Repository Layout

```text
README.md
AGENTS.md
design.md
spec.md
interop-audit.md
schemas/
  mandate.schema.json
  profile.schema.json
  action-evaluation.schema.json
  evidence-event.schema.json
bindings/
  a2a.md
  mcp.md
  rest.md
  ucp-ap2.md
examples/
  profile.json
  marketplace-buyer.json
  marketplace-seller.json
  delegated-shopping.json
test-vectors/
  valid-marketplace-buyer.json
  invalid-over-budget-action.json
security.md
privacy.md
governance.md
glossary.md
conformance.md
```

## Normative Source

For v0.1, `spec.md` defines protocol behavior and `schemas/*.schema.json`
define the canonical machine-readable payloads. If prose and schema conflict,
the stricter requirement applies until the conflict is resolved.

## Design Boundary

AUMP is the user mandate layer. It should not define:

- payment authorization;
- merchant-of-record obligations;
- transport security primitives already owned by MCP, A2A, UCP, OAuth, or AP2;
- model internals or chain-of-thought disclosure;
- a universal ranking algorithm for user utility.

Instead, AUMP provides a portable, auditable contract that those systems can
reference.

# AUMP Design

## Problem

Agentic commerce and marketplace systems need agents to represent people in
negotiations, purchases, sales, service booking, and multi-party workflows.
Existing protocols cover adjacent layers:

- MCP standardizes how agents access tools, data, and services.
- A2A standardizes how agents discover each other and collaborate on tasks.
- UCP standardizes commerce capabilities such as catalog, cart, checkout, and
  orders.
- AP2 standardizes payment-related intent, cart, and payment mandates.

What remains underspecified is the representation contract before the agent
acts: what the user wants, what the agent may reveal, how it should bargain,
what it may trade off, when it must ask, and what evidence should be retained.

AUMP defines that missing layer.

## Goals

- Make user delegation explicit and portable across agents, platforms, and
  marketplaces.
- Represent both hard constraints and soft preferences.
- Provide concrete negotiation policy without requiring a specific model or
  bargaining algorithm.
- Prevent accidental disclosure of sensitive preference information such as max
  budget, urgency, or private constraints.
- Make escalation deterministic enough for implementers to test.
- Produce audit evidence that can be referenced by UCP/AP2 and dispute flows.
- Compose with MCP, A2A, UCP, AP2, OAuth, JWS, JSON Schema, and future agent
  protocols instead of replacing them.

## Non-Goals

- AUMP is not a payment protocol.
- AUMP is not a checkout protocol.
- AUMP is not a model evaluation benchmark.
- AUMP is not a guarantee that an agent will achieve optimal results.
- AUMP does not require agents to disclose private chain-of-thought.
- AUMP does not define legal agency by itself. Implementers must map mandates
  to applicable law, terms, and platform rules.

## Mental Model

An AUMP mandate is the parent instruction artifact for agent representation.
It answers:

1. Who is represented?
2. Which agent is bound?
3. What is the task?
4. What can the agent do without asking?
5. What must the agent never do?
6. What should the agent optimize for?
7. What information can be shared with counterparties?
8. What events require escalation?
9. What evidence is needed afterward?

## Layering

```text
User intent and preferences
        |
        v
AUMP mandate
        |
        +--> MCP tool calls
        +--> A2A agent messages
        +--> UCP catalog/cart/checkout
        +--> AP2 intent/cart/payment mandates
```

AUMP should be referenced by downstream protocols through an identifier and a
content hash, not copied into every downstream payload.

## Core Objects

### Mandate

The top-level representation contract. It includes the principal, bound agent,
purpose, authority, preferences, negotiation policy, disclosure policy,
escalation policy, evidence policy, and optional signatures.

### Profile

A discovery document for an AUMP issuer, verifier, platform, or agent runtime.
It advertises supported AUMP versions, schema URLs, binding support, signing
keys, and revocation endpoints.

### Evidence Event

A compact event emitted during execution. It can record an offer, counteroffer,
decision, escalation, refusal, or final result without exposing private model
reasoning.

## Authority Modes

- `advisory`: agent can recommend actions but cannot commit the user.
- `supervised`: agent can negotiate but must ask before commitment.
- `delegated`: agent can commit within the explicit mandate bounds.
- `blocked`: mandate is suspended, revoked, expired, or otherwise inactive.

## Preference Model

AUMP separates preferences into:

- hard constraints: must never be violated;
- soft preferences: weighted or ranked desires;
- dealbreakers: conditions requiring refusal;
- substitutes: acceptable alternatives;
- private notes: usable by the agent but not disclosable unless allowed.

This separation matters because marketplace agents may gain advantage by
learning private reservation prices, urgency, or substitution willingness.

## Negotiation Model

AUMP does not standardize a bargaining algorithm. It standardizes the policy
surface an algorithm must honor:

- opening posture;
- target and reservation values;
- concession cadence;
- bundling and trade acceptance;
- walk-away conditions;
- maximum rounds;
- allowed tone and counterpart behavior constraints.

## Disclosure Model

Agents need a strict boundary between internal preference use and external
communication. AUMP disclosure policy is deny-by-default for sensitive fields:
the agent may use protected information for decision-making but must not reveal
it unless the mandate explicitly permits disclosure.

## Escalation Model

Escalation rules are deterministic triggers. Examples:

- final price exceeds budget;
- item only partially satisfies hard constraints;
- counterparty asks for protected information;
- transaction becomes irreversible;
- agent confidence falls below a threshold;
- legal, safety, identity, or payment review is required;
- downstream UCP/AP2 requires user review.

## Evidence Model

The evidence log should be useful for user review and dispute resolution while
minimizing sensitive data retention. AUMP evidence should prefer:

- event summaries;
- immutable hashes of transcripts or payloads;
- mandate version references;
- decision categories;
- externally visible offers and acceptances.

It should avoid storing raw private data unless the mandate requires it and the
storage system is authorized.

## Why AUMP Is Not AP2

AP2 is transaction-centered. It proves that a payment-related action is
authorized and binds intent, cart, and payment artifacts.

AUMP is representation-centered. It exists earlier and wider than payment:
marketplace browsing, seller negotiation, service booking, subscription
cancellation, support escalation, price watching, procurement, and any task
where an agent represents user preferences.

AP2 may reference an AUMP mandate as the upstream representation mandate.

## Version Strategy

Version strings use date-based versions for draft snapshots and semver for
stable releases:

- `2026-04-25-draft`
- `0.1.0`
- `1.0.0`

Breaking changes before `1.0.0` are allowed but should include migration notes.

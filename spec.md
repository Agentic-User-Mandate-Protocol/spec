# Agentic User Mandate Protocol Specification

## 1. Status

This document defines AUMP v0.1 draft.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**,
**SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this
document are to be interpreted as described in RFC 2119 and RFC 8174.

## 2. Scope

AUMP defines a portable mandate format and protocol behavior for agents acting
on behalf of a user, organization, household, or other principal.

AUMP covers:

- authority boundaries;
- user preferences;
- negotiation policy;
- privacy and disclosure policy;
- escalation policy;
- representation evidence;
- binding references to MCP, A2A, UCP, AP2, and other protocols.

AUMP does not define payment execution, checkout state machines, agent-to-agent
transport, or tool invocation semantics.

## 3. Terminology

- **Principal**: The person or entity represented by the agent.
- **Agent**: The system bound by the mandate.
- **Mandate**: The representation contract issued by or for a principal.
- **Issuer**: The party that creates and signs a mandate.
- **Verifier**: The party that validates a mandate.
- **Counterparty**: Any external party the agent interacts with while acting
  under a mandate.
- **Protected Field**: A field that may be used for decision-making but must
  not be disclosed unless policy permits it.
- **Escalation**: A required handoff to the principal or another trusted
  approval channel.

## 4. Normative Artifacts

The canonical AUMP v0.1 data schemas are:

- `schemas/mandate.schema.json`
- `schemas/profile.schema.json`
- `schemas/action-evaluation.schema.json`
- `schemas/evidence-event.schema.json`

Implementations MUST validate mandates against the applicable schema version
before relying on them for autonomous action.

## 5. Mandate Lifecycle

A mandate status MUST be one of:

- `draft`: not active and not usable for action;
- `presented`: summarized to the principal but not activated;
- `active`: usable within scope;
- `suspended`: temporarily inactive;
- `revoked`: permanently inactive;
- `expired`: past `expires_at`;
- `superseded`: replaced by a newer mandate.

Agents MUST NOT act autonomously under mandates whose status is not `active`.

Agents MAY continue read-only analysis under `draft` or `presented` mandates if
the platform policy allows it.

## 6. Mandate Object

An AUMP mandate MUST include:

- `aump.version`;
- `id`;
- `status`;
- `issued_at`;
- `expires_at`;
- `principal`;
- `agent`;
- `purpose`;
- `authority`;
- `preferences`;
- `negotiation`;
- `disclosure`;
- `escalation`;
- `evidence`.

### 6.1 Principal

The `principal` object identifies the represented party. It SHOULD avoid
unnecessary personally identifying information.

### 6.2 Agent

The `agent` object binds the mandate to an agent or runtime. If the mandate is
intended for a specific deployed agent, `agent.id` MUST be stable.

### 6.3 Purpose

The `purpose` object defines the task and domain. Agents MUST treat purpose as
a scope boundary, not merely a hint.

### 6.4 Authority

The `authority.mode` field controls autonomy:

- `advisory`: no commitments;
- `supervised`: negotiation allowed, final commitment requires approval;
- `delegated`: commitment allowed within explicit bounds.

If `authority.mode` is `delegated`, the mandate MUST define at least one
objective bound such as budget, allowed counterparties, allowed categories,
time window, or prohibited action list.

### 6.5 Preferences

Hard constraints MUST be enforced before soft preferences.

Agents MUST NOT trade away a hard constraint unless the mandate explicitly
states that the constraint is negotiable and defines the escalation condition.

### 6.6 Negotiation

Negotiation policy controls how the agent bargains. Agents MUST NOT disclose
private negotiation fields, including reservation values, unless disclosure
policy explicitly allows it.

Agents MUST refuse or escalate when a counterparty offer violates a
walk-away condition.

### 6.7 Disclosure

Disclosure policy MUST be evaluated before every outbound message or payload
that contains mandate-derived information.

Fields marked as protected MUST NOT be disclosed to counterparties unless an
allow rule matches the field and counterparty context.

### 6.8 Escalation

Escalation policy defines conditions that require trusted review. Agents MUST
pause commitment actions when escalation is required.

Escalation SHOULD produce a user-readable summary containing:

- proposed action;
- reason escalation is required;
- material terms;
- mandate fields involved;
- available choices.

### 6.9 Evidence

Agents SHOULD append evidence events for material negotiation steps,
escalations, refusals, and commitments.

Evidence events SHOULD store summaries and hashes rather than raw private
content unless policy requires raw content retention.

Evidence events SHOULD validate against
`schemas/evidence-event.schema.json`.

If a mandate evidence policy uses `summary_only` or `hashes` retention,
evidence events MUST NOT contain private fields. If private content is retained
under an explicit `full_transcript` policy, the event MUST mark
`privacy.contains_private_fields` as `true` and access to the event MUST be
controlled as sensitive mandate data.

## 7. Discovery

AUMP issuers, verifiers, platforms, or agent runtimes SHOULD publish a profile
document at:

```text
https://example.com/.well-known/aump
```

The profile document MUST validate against `schemas/profile.schema.json`.

## 8. Abstract Operations

AUMP defines the following abstract operations. Transport bindings MAY expose
these operations over REST, MCP tools, A2A messages, or platform-native APIs.

### 8.1 Create Mandate

Creates a `draft` mandate from intake data.

The issuer SHOULD produce both a full mandate and a user-readable summary.

### 8.2 Present Mandate

Presents a summary to the principal. If the principal accepts the summary, the
mandate MAY move to `active`.

### 8.3 Evaluate Action

Evaluates a proposed agent action against an active mandate.

The result MUST be one of:

- `allowed`;
- `requires_escalation`;
- `denied`.

Evaluate Action requests and responses SHOULD validate against
`schemas/action-evaluation.schema.json`.

### 8.4 Append Evidence

Adds an evidence event to the mandate log or an external evidence store.

Append Evidence payloads SHOULD validate against
`schemas/evidence-event.schema.json`. Runtime implementations SHOULD reject
events whose `mandate_ref.id` does not match the mandate being used or whose
event type is outside the mandate evidence policy.

### 8.5 Revoke Mandate

Revokes a mandate. Agents MUST stop autonomous action as soon as revocation is
known.

### 8.6 Resolve Mandate

Retrieves the active mandate or profile by ID, hash, or reference URL.

## 9. Binding References

Downstream protocols SHOULD reference an AUMP mandate using:

- `aump_mandate_id`;
- `aump_mandate_hash`;
- `aump_mandate_url` when appropriate;
- `aump_version`.

Implementations SHOULD NOT embed full private AUMP mandates in counterparty
payloads unless the disclosure policy allows it.

## 10. Security Requirements

Implementations MUST:

- verify schema validity before use;
- check status and expiration;
- enforce hard constraints before optimization;
- enforce disclosure rules before outbound communication;
- treat mandates and evidence as sensitive data;
- support revocation;
- produce audit events for commitments and escalations.

Implementations SHOULD:

- sign active mandates;
- use JSON Canonicalization Scheme before signing JSON payloads;
- use HTTPS for profile and mandate resolution;
- keep public summaries separate from private policy fields.

## 11. Privacy Requirements

Platforms MUST NOT assume consent to store or share preferences outside the
mandate's disclosure and evidence policies.

Mandates SHOULD minimize personal data. Preference and negotiation fields often
reveal sensitive information even when they are not traditional identifiers.

## 12. Error Codes

Implementations SHOULD use these standard error codes:

| Code | Meaning |
| --- | --- |
| `mandate_not_found` | Mandate cannot be resolved. |
| `mandate_inactive` | Mandate is not active. |
| `mandate_expired` | Mandate is past `expires_at`. |
| `mandate_revoked` | Mandate has been revoked. |
| `schema_invalid` | Payload does not match schema. |
| `scope_violation` | Proposed action is outside purpose or authority. |
| `hard_constraint_violation` | Proposed action violates a hard constraint. |
| `disclosure_denied` | Outbound message would reveal protected data. |
| `escalation_required` | Trusted review is required. |
| `signature_invalid` | Mandate signature failed verification. |

## 13. Conformance

An implementation is AUMP v0.1 conformant if it can:

1. parse and validate `schemas/mandate.schema.json`;
2. parse and validate `schemas/action-evaluation.schema.json`;
3. enforce mandate status and expiration;
4. evaluate hard constraints before soft preferences;
5. enforce disclosure rules on outbound payloads;
6. return `allowed`, `requires_escalation`, or `denied` for proposed actions;
7. process revocation;
8. emit evidence for material commitments.

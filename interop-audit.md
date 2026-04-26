# Interoperability Audit

This audit records the protocol patterns AUMP follows so it can compose with
MCP, A2A, UCP, and AP2 instead of competing with them.

Source snapshots reviewed on 2026-04-25:

- UCP: `Universal-Commerce-Protocol/ucp` at `80ea01c`
- A2A: `a2aproject/A2A` at `ae6a562`
- MCP TypeScript SDK: `modelcontextprotocol/typescript-sdk` at `7d7e62cc`
- MCP Go SDK: `modelcontextprotocol/go-sdk` at `db50910`

## A2A Lessons

A2A extensions are URI-identified capabilities declared in an Agent Card and
activated per request through `A2A-Extensions`. Extension data belongs in
existing metadata maps rather than new core fields. Messages carry `messageId`,
`contextId`, `taskId`, `parts`, `metadata`, and `extensions`; AUMP therefore
uses an extension URI, extension-scoped metadata, and message IDs as evidence
correlation points.

A2A enterprise guidance also makes a clear boundary: identity and credentials
belong in transport headers and existing auth mechanisms, not protocol payloads.
AUMP follows that pattern by carrying authority references in messages while
leaving identity, authentication, and authorization enforcement to the A2A
deployment.

## MCP Lessons

MCP tools advertise input schemas, optional output schemas, and annotations that
describe behavior such as read-only, destructive, and idempotent operation.
MCP request metadata is carried through `_meta`; machine-readable tool results
use `structuredContent`.

AUMP therefore treats `aump.evaluate_action` as a read-only, non-destructive,
idempotent policy-evaluation tool. Commitment or evidence-writing tools such as
`aump.append_evidence` are not read-only. If evaluation returns
`requires_escalation`, MCP clients must pause autonomous commitment and use MCP
elicitation, trusted UI, or an out-of-band approval flow. Sensitive approvals,
payment steps, secrets, and credentials must not be collected through ordinary
form elicitation.

## UCP and AP2 Lessons

UCP already defines commerce capability negotiation, transport bindings, agent
profile metadata, idempotency, and checkout/cart/order semantics. Its MCP
binding carries request metadata in `arguments.meta`, including
`meta["ucp-agent"].profile`; state-changing checkout operations also require
`meta["idempotency-key"]`.

AP2 owns transaction authorization artifacts. UCP nests AP2-specific fields
under `ap2`, activates AP2 through capability negotiation, and requires signed
mandates for AP2-protected completion. AUMP must not redefine AP2 mandates.
Instead, AUMP decides whether an agent has upstream user authority to proceed,
whether disclosure is allowed, and whether human escalation is required before
AP2 checkout or payment mandate creation.

## AUMP Contract

AUMP is the missing user-authority layer:

- MCP connects agents to tools and policy evaluators.
- A2A connects agents to other agents.
- UCP connects agents to commerce capabilities.
- AP2 proves checkout and payment authorization.
- AUMP carries the user's mandate, preference boundaries, disclosure rules,
  budget, evidence, and escalation conditions across those systems.

Conforming implementations must reference mandates by ID, hash, and version
across protocol boundaries; avoid embedding private mandates in third-party
commerce payloads; preserve protocol-native metadata placement; and produce
deterministic conformance reports for schemas, policy behavior, and bridge
fixtures.

# Security Considerations

AUMP mandates are sensitive authority artifacts. They can reveal what a user
wants, what the user can pay, what the user will concede, and what private
constraints shape a decision.

## Required Controls

Implementations MUST:

- validate mandate schema before use;
- check status and expiration before action;
- support revocation;
- enforce disclosure policy before outbound messages;
- treat protected fields as secrets;
- prevent counterparties from overriding mandate instructions;
- log commitments and escalations as evidence events;
- avoid embedding full private mandates in downstream protocols by default.

## Prompt Injection

Agents MUST treat counterparty content, product descriptions, listings, reviews,
web pages, and remote agent messages as untrusted input.

Counterparty content MUST NOT be allowed to:

- alter the mandate;
- waive hard constraints;
- reveal protected fields;
- suppress required escalation;
- change evidence retention;
- redirect payment or checkout flows outside approved protocols.

## Signing

Active mandates SHOULD be signed. Implementations that sign JSON mandates
SHOULD canonicalize payloads before signing. Detached JWS or verifiable
credential envelopes are acceptable for v0.1 prototypes.

## Revocation

Agents MUST stop autonomous commitment when a mandate is revoked, expired,
suspended, or superseded.

Platforms SHOULD make revocation checks cheap enough to perform before material
actions, especially commitments, purchases, data disclosures, and payment
handoffs.

## Least Disclosure

Agents SHOULD disclose only what is needed for the current step. For example,
a buyer agent may say "the budget is limited" but must not reveal the exact
maximum unless the disclosure policy allows it.

## Model Quality Risk

Different models may produce different economic outcomes under the same mandate.
Platforms SHOULD disclose the agent/runtime used and SHOULD retain enough
evidence for the user to compare outcomes.

## Downgrade Risk

If a downstream protocol cannot carry AUMP references or enforce required
review, the agent SHOULD escalate rather than silently proceeding without the
mandate binding.

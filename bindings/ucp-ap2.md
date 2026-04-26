# UCP and AP2 Binding

UCP and AP2 own checkout, payment, cart, order, and payment mandate semantics.
AUMP composes with them as an upstream representation mandate.

## Placement

UCP payloads SHOULD reference an AUMP mandate in the protocol metadata envelope:

```json
{
  "meta": {
    "ucp-agent": {
      "profile": "https://platform.example/profiles/shopping-agent.json"
    },
    "aump": {
      "mandate_id": "aump_mnd_shop_001",
      "mandate_hash": "sha256-example",
      "version": "0.1.0"
    }
  },
  "checkout": {
    "id": "checkout_123",
    "status": "incomplete"
  }
}
```

For UCP over MCP, this is the UCP `arguments.meta` object, not MCP request
`params._meta`. For UCP over A2A, the same AUMP reference SHOULD be carried in
extension-scoped A2A metadata and any UCP data part SHOULD avoid duplicating
private mandate fields.

The full private AUMP mandate SHOULD NOT be sent to merchants, PSPs, networks,
or credential providers unless disclosure policy explicitly allows it.

## AP2 Relationship

AP2 intent, cart, and payment mandates can prove transaction authorization.
AUMP proves upstream representation authority and preference constraints.
AUMP fields MUST NOT be placed under AP2's `ap2` object. AP2's namespace is
reserved for AP2 merchant authorization, checkout mandates, payment mandates,
and AP2-defined error semantics.

Recommended chain:

```text
AUMP mandate -> AP2 intent mandate -> AP2 cart mandate -> AP2 payment mandate
```

Before an agent creates an AP2 checkout or payment mandate, it SHOULD evaluate
the relevant AUMP mandate action. If AUMP returns `requires_escalation`, the
agent MUST complete trusted user review before creating or submitting the AP2
mandate.

## UCP Relationship

UCP checkout contexts MAY include non-sensitive AUMP-derived intent summaries.
They MUST NOT include protected fields such as reservation price, urgency, or
private constraints unless disclosure policy allows it.

State-changing UCP operations SHOULD preserve UCP idempotency requirements
independently from AUMP evidence identifiers. AUMP evidence SHOULD record the
UCP idempotency key when one exists, but it does not replace that key.

## Escalation

If UCP returns a checkout state requiring buyer review, or if AP2 requires a
trusted user signature, the AUMP agent MUST treat that as an escalation event
unless the mandate already required trusted UI review for that exact action.

## Evidence

When a UCP checkout or AP2 mandate is created from an AUMP mandate, the agent
SHOULD append evidence containing:

- AUMP mandate ID and hash;
- downstream protocol and object ID;
- action summary;
- timestamp;
- result status;
- hash of the downstream payload when available.

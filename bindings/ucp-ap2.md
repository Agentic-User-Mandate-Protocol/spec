# UCP and AP2 Binding

UCP and AP2 own checkout, payment, cart, order, and payment mandate semantics.
AUMP composes with them as an upstream representation mandate.

## Placement

UCP or AP2 payloads SHOULD reference an AUMP mandate using:

```json
{
  "aump": {
    "mandate_id": "aump_mnd_shop_001",
    "mandate_hash": "sha256-example",
    "version": "0.1.0"
  }
}
```

The full private AUMP mandate SHOULD NOT be sent to merchants, PSPs, networks,
or credential providers unless disclosure policy explicitly allows it.

## AP2 Relationship

AP2 intent, cart, and payment mandates can prove transaction authorization.
AUMP proves upstream representation authority and preference constraints.

Recommended chain:

```text
AUMP mandate -> AP2 intent mandate -> AP2 cart mandate -> AP2 payment mandate
```

## UCP Relationship

UCP checkout contexts MAY include non-sensitive AUMP-derived intent summaries.
They MUST NOT include protected fields such as reservation price, urgency, or
private constraints unless disclosure policy allows it.

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

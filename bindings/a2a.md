# A2A Binding

A2A is the preferred binding when two agents need to communicate while one or
both agents are acting under an AUMP mandate.

## Agent Card Extension

An A2A Agent Card MAY advertise AUMP support through an extension entry:

```json
{
  "capabilities": {
    "extensions": [
      {
        "uri": "https://agentic-user-mandate-protocol.github.io/spec/bindings/a2a",
        "description": "Supports AUMP mandate references and action evaluation.",
        "required": false
      }
    ]
  }
}
```

## Message Metadata

A2A messages SHOULD reference mandates by ID and hash rather than embedding the
full mandate:

```json
{
  "message": {
    "role": "user",
    "parts": [{ "text": "Negotiate for this listing." }],
    "metadata": {
      "aump_mandate_id": "aump_mnd_market_001",
      "aump_mandate_hash": "sha256-example"
    }
  }
}
```

## Disclosure Boundary

An agent MUST apply the AUMP disclosure policy before sending any A2A message
to another agent. This includes messages to merchant agents, seller agents,
broker agents, and credential provider agents.

## Task Lifecycle

For long-running A2A tasks, the mandate reference SHOULD remain attached to the
task context or task metadata. If the mandate becomes revoked, expired, or
suspended, the agent SHOULD cancel or pause active A2A tasks that depend on it.

## Recommended Evidence Events

A2A agents SHOULD append evidence when:

- a task is created under a mandate;
- a counterparty makes a material offer;
- the agent makes a counteroffer;
- a proposed action is denied by mandate policy;
- escalation is requested;
- a deal is accepted, rejected, or abandoned.

# A2A Binding

A2A is the preferred binding when two agents need to communicate while one or
both agents are acting under an AUMP mandate.

## Agent Card Extension

An A2A Agent Card MAY advertise AUMP support through an extension entry:

```json
{
  "name": "Example Buyer Agent",
  "description": "Agent that negotiates under portable user mandates.",
  "version": "0.1.0",
  "url": "https://agent.example.com/a2a",
  "capabilities": {
    "extensions": [
      {
        "uri": "https://agentic-user-mandate-protocol.github.io/spec/bindings/a2a/v0.1",
        "description": "Supports AUMP mandate references and action evaluation.",
        "required": false,
        "params": {
          "versions": ["0.1.0"]
        }
      }
    ]
  },
  "defaultInputModes": ["text/plain", "application/json"],
  "defaultOutputModes": ["text/plain", "application/json"],
  "skills": [
    {
      "id": "mandated-negotiation",
      "name": "Mandated negotiation",
      "description": "Negotiate while enforcing an AUMP mandate.",
      "tags": ["aump", "negotiation"]
    }
  ]
}
```

AUMP uses a versioned A2A extension URI. Breaking changes to the A2A binding
MUST use a new URI.

Clients SHOULD activate the extension on requests with the `A2A-Extensions`
HTTP header. Agents SHOULD echo successfully activated extensions in the
response `A2A-Extensions` header.

## Message Metadata

A2A messages SHOULD reference mandates by ID and hash rather than embedding the
full mandate:

```json
{
  "headers": {
    "A2A-Extensions": "https://agentic-user-mandate-protocol.github.io/spec/bindings/a2a/v0.1"
  },
  "message": {
    "messageId": "msg_001",
    "role": "user",
    "parts": [{ "text": "Negotiate for this listing." }],
    "extensions": [
      "https://agentic-user-mandate-protocol.github.io/spec/bindings/a2a/v0.1"
    ],
    "metadata": {
      "https://agentic-user-mandate-protocol.github.io/spec/bindings/a2a/v0.1": {
        "mandate_id": "aump_mnd_market_001",
        "mandate_hash": "sha256-example",
        "version": "0.1.0",
        "evidence_id": "ev_001"
      }
    }
  }
}
```

The message `metadata` entry SHOULD be keyed by the AUMP extension URI so it
does not collide with other A2A extensions. The `messageId` remains the A2A
idempotency key for retries; AUMP implementations SHOULD include it in evidence
records for material actions.

Agents MAY support the legacy `message.metadata.aump_mandate_id` and
`message.metadata.aump_mandate_hash` keys while migrating, but conforming v0.1
fixtures use extension-scoped metadata.

## Disclosure Boundary

An agent MUST apply the AUMP disclosure policy before sending any A2A message
to another agent. This includes messages to merchant agents, seller agents,
broker agents, and credential provider agents.

An A2A message MUST NOT include the full private AUMP mandate unless the
recipient is authorized to receive every field under the mandate disclosure
policy. The default interoperable form is a mandate ID, hash, and version.

## Task Lifecycle

For long-running A2A tasks, the mandate reference SHOULD remain attached to the
task context or task metadata using the AUMP extension URI. If the mandate becomes revoked, expired, or
suspended, the agent SHOULD cancel or pause active A2A tasks that depend on it.

## Recommended Evidence Events

A2A agents SHOULD append evidence when:

- a task is created under a mandate;
- a counterparty makes a material offer;
- the agent makes a counteroffer;
- a proposed action is denied by mandate policy;
- escalation is requested;
- a deal is accepted, rejected, or abandoned.

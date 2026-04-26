# MCP Binding

MCP is the preferred binding when an agent runtime needs to evaluate or resolve
AUMP mandates while using tools.

## Capability Shape

An AUMP-aware MCP server SHOULD expose tools such as:

- `aump.resolve_mandate`
- `aump.evaluate_action`
- `aump.append_evidence`
- `aump.revoke_mandate`

Tool responses SHOULD return structured JSON that validates against the AUMP
schemas or a small operation result object.

`aump.evaluate_action` SHOULD be advertised as a read-only, non-destructive,
idempotent MCP tool with input and output schemas. Tools that mutate mandate
state, append evidence, revoke authority, or perform commitment are not
read-only.

Example tool definition:

```json
{
  "name": "aump.evaluate_action",
  "description": "Evaluate a proposed action against an AUMP mandate.",
  "inputSchema": {
    "type": "object",
    "required": ["mandate_ref", "proposed_action"],
    "properties": {
      "mandate_ref": { "type": "object" },
      "proposed_action": { "type": "object" },
      "context": { "type": "object" }
    }
  },
  "outputSchema": {
    "type": "object",
    "required": ["decision", "reason_codes"],
    "properties": {
      "decision": {
        "enum": ["allowed", "denied", "requires_escalation"]
      },
      "reason_codes": {
        "type": "array",
        "items": { "type": "string" }
      }
    }
  },
  "annotations": {
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": true
  }
}
```

## Metadata

MCP clients and servers MAY include AUMP references in `_meta` fields:

```json
{
  "method": "tools/call",
  "params": {
    "name": "aump.evaluate_action",
    "_meta": {
      "org.agentic-user-mandate-protocol/aump_mandate_id": "aump_mnd_market_001",
      "org.agentic-user-mandate-protocol/aump_mandate_hash": "sha256-example",
      "org.agentic-user-mandate-protocol/aump_version": "0.1.0"
    },
    "arguments": {
      "mandate_ref": {
        "id": "aump_mnd_market_001",
        "hash": "sha256-example",
        "version": "0.1.0"
      },
      "proposed_action": {
        "type": "accept_deal"
      }
    }
  }
}
```

When an MCP server is also implementing a UCP capability, UCP request metadata
remains in tool `arguments.meta`. AUMP request metadata MAY still use MCP
`params._meta`; AUMP references inside UCP payloads SHOULD use `meta.aump`.

## Requirements

- MCP tools MUST NOT return private mandate fields to a caller that is not
  authorized to see them.
- MCP servers SHOULD expose a redacted public mandate summary separately from
  the full mandate.
- Tool calls that would commit the principal SHOULD call `aump.evaluate_action`
  first or otherwise perform equivalent mandate evaluation.
- If mandate evaluation returns `requires_escalation`, the MCP client MUST NOT
  continue autonomous commitment until escalation completes.
- Human escalation SHOULD use MCP elicitation, trusted UI, or an out-of-band
  approval flow. Secrets, payment credentials, and high-risk approval artifacts
  MUST NOT be collected through ordinary form elicitation.
- Tool results SHOULD populate `structuredContent` with the machine-readable
  AUMP decision object.

## Example Operation Result

```json
{
  "status": "requires_escalation",
  "mandate_id": "aump_mnd_market_001",
  "reason": "Offer exceeds max_total_minor",
  "paths": ["$.authority.budget.max_total_minor"]
}
```

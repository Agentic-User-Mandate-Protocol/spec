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

## Metadata

MCP clients and servers MAY include AUMP references in `_meta` fields:

```json
{
  "_meta": {
    "org.agentic-user-mandate-protocol/aump_mandate_id": "aump_mnd_market_001",
    "org.agentic-user-mandate-protocol/aump_mandate_hash": "sha256-example"
  }
}
```

## Requirements

- MCP tools MUST NOT return private mandate fields to a caller that is not
  authorized to see them.
- MCP servers SHOULD expose a redacted public mandate summary separately from
  the full mandate.
- Tool calls that would commit the principal SHOULD call `aump.evaluate_action`
  first or otherwise perform equivalent mandate evaluation.
- If mandate evaluation returns `requires_escalation`, the MCP client MUST NOT
  continue autonomous commitment until escalation completes.

## Example Operation Result

```json
{
  "status": "requires_escalation",
  "mandate_id": "aump_mnd_market_001",
  "reason": "Offer exceeds max_total_minor",
  "paths": ["$.authority.budget.max_total_minor"]
}
```

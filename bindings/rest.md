# REST Binding

The REST binding is a simple implementation option for platforms that do not
want to expose AUMP through MCP or A2A.

## Discovery

Profiles SHOULD be published at:

```text
GET /.well-known/aump
```

## Endpoints

```text
POST /aump/mandates
GET  /aump/mandates/{id}
POST /aump/mandates/{id}:present
POST /aump/mandates/{id}:evaluateAction
POST /aump/mandates/{id}:appendEvidence
POST /aump/mandates/{id}:revoke
```

## Evaluate Action

Request and response bodies SHOULD validate against
`schemas/action-evaluation.schema.json`.

## Status Codes

| HTTP status | Use |
| --- | --- |
| `200` | Evaluation, retrieval, or mutation succeeded. |
| `201` | Mandate created. |
| `400` | Payload is invalid. |
| `401` | Authentication required. |
| `403` | Caller lacks access to mandate. |
| `404` | Mandate not found. |
| `409` | Mandate state prevents operation. |
| `410` | Mandate revoked or expired. |

## Requirements

- REST deployments MUST use HTTPS in production.
- Servers MUST authorize access before revealing mandate existence.
- Servers SHOULD return redacted views by default unless the caller is allowed
  to view private fields.

# Governance

AUMP v0.1 is a draft under the Agentic User Mandate Protocol organization.

## Change Process

- Minor editorial changes MAY be merged without version changes.
- Schema changes MUST update examples and test vectors.
- Breaking changes SHOULD include migration notes.
- Stable releases SHOULD be tagged.

## Versioning

Drafts MAY use date-based labels. Stable releases SHOULD use semantic versions.

Examples:

- `2026-04-25-draft`
- `0.1.0`
- `1.0.0`

## Compatibility

Before `1.0.0`, breaking changes are allowed. After `1.0.0`, implementations
SHOULD preserve backward compatibility within a major version.

## Namespaces

Until a permanent domain is selected, examples use:

```text
https://agentic-user-mandate-protocol.github.io/spec/
org.agentic-user-mandate-protocol/*
```

If a permanent domain is adopted later, the project SHOULD publish redirects
and migration guidance.

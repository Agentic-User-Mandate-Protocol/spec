# Privacy Considerations

AUMP preferences are sensitive even when they do not contain direct identifiers.
Budget, urgency, willingness to compromise, medical needs, family details,
gift intent, location, and substitution preferences can all expose private
information.

## Data Minimization

Mandates SHOULD contain the minimum information needed for the agent to act.

Public summaries SHOULD be separate from private policy fields.

## Consent

Consent to use a mandate for one task MUST NOT be treated as consent to reuse
the mandate for unrelated tasks.

Consent to store preferences MUST NOT be treated as consent to share
preferences with counterparties.

## Disclosure Classes

AUMP implementations SHOULD classify fields as:

- public: safe to show counterparties;
- operational: usable by the agent and platform;
- protected: usable for decisions but not disclosable;
- restricted: requires explicit escalation before use or disclosure.

## Evidence Retention

Evidence logs SHOULD prefer summaries and hashes over raw transcripts.

If raw transcripts are retained, the mandate SHOULD state retention period,
access policy, deletion rights, and whether private fields may be stored.

## User Access

Principals SHOULD be able to inspect:

- active mandates;
- mandate summaries;
- revocation status;
- material evidence events;
- downstream protocol references created from the mandate.

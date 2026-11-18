# Requirements clarifications and open decisions

The original description remains unchanged in `OPERATOR-INTENT.md`. This record
summarizes subsequent discussion; it is not a verbatim transcript or evidence
that any mechanism has been implemented or validated.

## Established direction

- Transport can be anything: direct client exchange and posting to public servers
  are both required. The exact directive is appended verbatim in
  `OPERATOR-INTENT.md`. Neither HTTPS nor the public text feed is a mandatory
  universal carrier; implementations must describe their actual capabilities.

- Payloads are actually encrypted. Appropriate key material allows recipients to
  decrypt the matching GPS coordinates, chats and alerts.
- The public site remains text-based: it carries encoded group ciphertext, not
  readable group content. A separate public unencrypted-content mode is not a
  current requirement.
- People must be able to share access. The precise sharing/admission mechanism is
  undecided; a permanent key that unlocks every future update is not assumed.
- Forward secrecy and recovery after compromise are requirements to evaluate.
  Rotation must exclude compromised access rather than hand fresh keys to it.
- Records have limited serving availability. Retained recipient copies cannot
  be forcibly erased by a server-side expiry deadline.
- Keep the architecture open to changing encryption. No algorithm, protocol or
  library is selected permanently, including MLS.

## Proposed design responses, not fixed operator choices

`ENCRYPTION-ARCHITECTURE.md` proposes separating application payloads, security
state, envelopes and delivery, with explicit versioning and migration gates.
Invitations, authenticated device membership and established group protocols are
research directions. These are not permission to invent cryptography or begin
implementation. Changing the mechanism may require revising these boundaries.

## Still open

- What exactly a shared artifact grants; automatic versus approved admission.
- Membership administration, sender attribution, device identity and recovery.
- History access, secret deletion, offline catch-up and control-message retention.
- Location matching and public metadata leakage budget.
- Expiry defaults, maximum lifetimes, storage cleanup and backup policy.
- Protocol/library selection and independent security review.
- Android delivery behavior, OAG integration and the meaning of “legacy.”

## State

Specification expanded; no external research, implementation, tests or deployments
have occurred for this project. Next: resolve admission and location-discovery
priorities, compare established encryption approaches, and assess Android support.
Record selection rationale and migration consequences without making any early
choice irreversible.

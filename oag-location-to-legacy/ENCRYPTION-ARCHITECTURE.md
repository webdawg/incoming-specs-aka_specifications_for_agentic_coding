# Changeable encryption architecture

Status: design requirements and proposed boundaries, not a selected protocol,
finished wire format, implementation, or cryptographic security review.

## 1. Stable product contract; revisable mechanism

People share access to actual encrypted location-linked data. The public site
serves text-encoded ciphertext. A recipient with the appropriate secret state can
decrypt the corresponding records locally. The service is not entrusted with
content decryption keys. A shared secret valid forever is not sufficient for the
forward-secrecy and compromise-recovery goals.

The operator explicitly requires leaving the architecture open to changing the
encryption approach. No protocol, algorithm suite, library, key topology,
invitation mechanism, or epoch scheme is permanently mandated. MLS is a research
candidate, not an architectural dependency already selected.

Changing the mechanism must preserve the agreed security contract or explicitly
surface a proposed change for review. Do not market a weaker mechanism under the
same guarantees merely because it fits an interface.

## 2. Required distinctions

| Concept | Meaning and boundary |
| --- | --- |
| Content key / secret state | What actually permits decryption of particular records. Never a public routing identifier. |
| Invitation / admission capability | Authorizes a joining exchange, if the selected design uses one. Not necessarily a decryption key and not a permanent right to every future key. |
| Group membership | Who is entitled to participate; distinguish people from their individual devices. |
| Routing reference | Lets infrastructure locate ciphertext without requiring its decryption secret; may still reveal linkability. |
| Protocol generation | A version of the cryptographic design or interoperable wire protocol. |
| Key generation / epoch | Changing secret state within a protocol; terminology and exact behavior depend on the chosen design. |
| Serving deadline | When this service stops delivering a record. Not a cryptographic self-destruct mechanism. |

Avoid one overloaded string that simultaneously acts as group ID, invitation,
content key and long-lived administrative authority. Determine the actual roles
and exposure of every field before selecting representations.

## 3. Security properties to preserve

- **Confidentiality and integrity:** authenticated encryption or an established
  protocol construction protects payloads and binds security-relevant context.
  Reject unauthenticated plaintext; successful decoding is not authentication.
- **Forward secrecy:** disclosure of current secret state must not expose earlier
  protected messages after their decryption secrets have been discarded, within
  the selected protocol's stated threat model. Retained history keys and plaintext
  caches are explicit exceptions, not magically protected history.
- **Post-compromise recovery:** specify the protocol transitions and fresh secret
  contribution needed to exclude an attacker from later messages. Remove compromised
  devices and revoke compromised admission capabilities where applicable. Continuing
  control of a participating device prevents a blanket recovery guarantee.
- **Membership transitions:** define joining, removal, device replacement and
  concurrent membership updates. Do not silently give newcomers old history.
- **Sender semantics:** distinguish “a key holder produced this” from authenticated
  attribution to one sender. Symmetric group-key possession alone may permit forgery.
- **Replay and state safety:** define duplicate, stale, reordered and forked state
  handling, including restored backups and nonce reuse hazards.

HTTPS remains necessary for client/service communication even when payloads are
end-to-end encrypted. Payload encryption does not hide all metadata, authenticate
the service endpoint, or protect control operations on its own.

## 4. Proposed separation of responsibilities

These are conceptual boundaries to test during research, not required classes,
RPCs, plugins or an assurance that protocols can be swapped without redesign.

1. **Domain payload:** coordinates, observation time, chat/alert content and their
   application schema. Constructed and interpreted on authorized clients.
2. **Group security engine:** protocol-specific membership, identity binding,
   message protection, control messages, state updates and verification. Owns
   secrets rather than passing raw keys through general application code.
3. **Client state store:** persists opaque, versioned protocol state with appropriate
   device protection. Records protocol identity and supports safe updates/recovery.
4. **Delivery envelope:** bounded public representation carrying routing and
   delivery information plus opaque protected content/control messages.
5. **Transport and service storage:** accepts, indexes, streams and expires envelopes
   without decrypting application payloads. Applies publish authorization, quotas
   and other metadata-level policies independently of content secrecy.
6. **User interface:** displays group membership and security state, shares access,
   and explains failures without exposing secret material or implying unsupported
   security guarantees.

An engine must expose lifecycle and capability information, not just `encrypt`
and `decrypt`. Examples include create/join, process control updates, protect/open,
remove a device, advance state, report unsupported generation and recover safely.
Exact operations must follow the selected protocol. A stateless cipher wrapper
cannot manufacture a secure distributed membership protocol.

Keep Android UI, public feed framing and OAG integration free of assumptions about
key lengths, MLS-specific structures or a single long-lived group key. Do not
require runtime loading of arbitrary encryption plugins. Versioned, reviewed
implementations released through normal software updates may be sufficient.

## 5. Public text envelope and metadata

The public feed carries printable serialized envelopes, not decrypted GPS. Possible
fields include envelope version, crypto-protocol identifier, opaque routing
reference, record ID, delivery timestamp, serving deadline and encoded protected
payload. These are candidates, not a wire schema; minimize fields and justify each
public disclosure before standardizing them.

- Version application schemas, envelope formats and security protocols separately
  where their compatibility requirements differ.
- Authenticate protocol/context binding and relevant headers according to the
  chosen protocol; do not trust an unauthenticated algorithm selector or timestamp.
- Keep exact coordinates and sensitive group/message details inside protected data.
- Treat group references, timing, sizes and cursors as metadata leakage even if they
  look random. Padding or rotation may be researched, not assumed to solve traffic
  analysis automatically.
- Enforce size, framing and parser limits before processing expensive crypto work.
- Distinguish malformed envelope, unsupported protocol, missing state, authentication
  failure, replay, expiry and unavailable key material. Public errors must not leak
  secrets or sensitive membership information.

The client rejects unsupported or disallowed generations with an actionable error.
It must never retry failed decryption by treating ciphertext as plaintext, silently
selecting an older insecure suite, or requesting the group secret from the server.

## 6. Sharing, offline devices and compromise

The user experience must remain easy to share, but research must decide what the
shared artifact grants: existing-record access, admission, ongoing membership,
publication authority or administrative rights. Show that scope to the user.

Reusable invitations need explicit expiration, revocation and admission policy.
When leaked, revoke the invitation; assess members admitted using it as well.
Revoking an invitation alone does not remove devices already in the group.
Sharing a raw history key deliberately transfers access to its matching records
and cannot be undone by rotating future keys.

For offline devices, specify how far behind they may fall, which control messages
are necessary, and when they must rejoin. Removing expired control state may make
later decryption impossible; keeping it indefinitely may violate retention goals.
Resolve that tradeoff explicitly. Do not recover by sending all historical keys
under a permanent invitation secret.

Restoring a client backup can restore stale membership state, old secrets or send
counters. Define a safe rejoin/recovery path and test for key/nonce reuse and replay.
Device keystore use alone does not settle backup, plaintext cache or compromised
application behavior.

## 7. Replacing encryption without silent breakage

Before adopting a different protocol or library, record a decision covering:

1. Why the change is needed, alternative candidates and security-property changes.
2. Supported old/new clients, on-wire versions and exact capability checks.
3. Whether groups update in place or form a new group with a fresh trust bootstrap.
4. Authenticated membership transfer and removal of compromised participants.
5. A cutover point, offline-member handling and explicit send/read rules on each side.
6. Old record readability, retained keys, retention deadlines and history consent.
7. Failure recovery and downgrade prevention. Rollback must not restore revoked
   members or compromised encryption merely to restore availability.
8. Independent cryptographic review and reproducible compatibility/security tests.

Do not automatically dual-publish new content under an old compromised scheme;
that defeats the new protection. Re-encrypting history does not revoke ciphertext
and keys already copied under the old scheme. Clearly label any retained legacy
read capability and its security limits.

Different schemes may not interoperate. An honest migration requiring a fresh
invitation is preferable to claiming transparent replacement without a secure
membership/state transition. Maintain an open architecture, not a promise of
cost-free cryptographic substitution.

## 8. Research and acceptance gates

Evaluate mature group protocols/libraries, Android support, identity assumptions,
offline behavior, post-compromise conditions and operational limits. A basic
shared-key design can serve as a comparison baseline, but must not pass acceptance
for forward secrecy or recovery it does not provide.

Required evaluation scenarios include:

- Correctly authorized devices decrypt and authenticate records; public readers
  lacking appropriate state cannot recover the payload.
- Captured current state does not decrypt earlier protected messages after intended
  key deletion; tests separately expose any deliberate history retention.
- A removed device cannot derive keys after the documented recovery transition;
  tests distinguish already-held records from later protected messages.
- Leaked invitations, concurrent senders, tampering, replay, reordering, missed
  updates, restart and stale backup restoration have defined outcomes.
- Unsupported protocol versions and attempted downgrades fail safely.
- Old/new client coexistence and migration do not silently disclose new content
  through the retired mechanism or reinstate removed members.
- Delivery expiry applies to ciphertext and queues, while documentation explicitly
  declines to promise erasure of recipient-held plaintext or secrets.

Testing supports implementation correctness; passing examples alone is not proof
of a cryptographic protocol's security. Require protocol analysis and appropriate
independent review before security claims or deployment.

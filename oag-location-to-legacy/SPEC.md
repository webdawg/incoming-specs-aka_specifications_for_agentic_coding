# OAG location to legacy

Status: initial requirements baseline for discussion. No implementation or external
research has been performed for this specification. Proposed structures below are
interpretations, not settled operator decisions.

License: AGPL-3.0-or-later.

## 1. Purpose

Build a public location-linking service: a client supplies a location and receives
relevant data. Chats, alerts and other records can be associated with places.
Provide a small Android application and a simple public plain-text interface that
lists location-linked records in time order and supports live streaming.

People can also “key together”: sharing key material enables a group to read
otherwise encrypted data. Records are available or served for a limited time.
The original description is preserved in [OPERATOR-INTENT.md](OPERATOR-INTENT.md).

The project name does not yet define what “legacy” means. Integration with OAG
nodes, mesh routing, storage or event protocols must be researched and specified;
no existing integration is assumed merely from the name.

## 2. Core concepts and boundaries

- **Location query:** where a client wants information. This need not be the
  client's current physical position; manual selection is a proposed option.
- **Record location:** the point, area or named place associated with content.
  It is not automatically the author's location or proof of physical presence.
- **Record:** content such as a chat message or alert, with time and location
  associations, visibility, and an availability deadline.
- **Public plaintext:** content intentionally readable without a group secret.
- **Publicly retrievable ciphertext:** encrypted content may travel through a
  public service while only key holders can decrypt it. This is not the same as
  public plaintext, and may still expose metadata.
- **Group:** people who gain access through a key-sharing or membership mechanism
  whose cryptographic design is still to be selected.
- **Expiry:** a defined limit on availability or serving. Its enforcement scope
  must be stated separately from deletion, key lifetime and recipient retention.

These are initial conceptual distinctions, not a final schema or wire protocol.

## 3. Primary flows

### A. Ask for data at a location

1. The client selects a place or explicitly authorizes obtaining device location.
2. It sends a location query and appropriate filters to the service.
3. The service matches eligible, unexpired records using a defined geographic rule.
4. The response presents public content and, if supported, discoverable encrypted
   records that the client may be able to decrypt locally.
5. The client may follow a live stream for later matching records.

Decide the geographic matching rule, precision, time window, pagination, and
whether encrypted records can be discovered without group membership.

### B. Publish a location-linked chat or alert

1. The publisher selects a location, record type, visibility and lifetime.
2. For a group record, the client encrypts before upload if end-to-end encryption
   is the selected model. The service must not be described as end-to-end encrypted
   if it receives plaintext or holds the relevant decryption keys.
3. The service validates the envelope and publishing permissions, applies bounds
   and abuse controls, and stores or distributes the accepted record.
4. Matching query results and subscribers receive the record according to policy.
5. At expiry, the serving policy stops new delivery and applies the selected
   storage cleanup policy.

The author identity, chat threading, alert categories, editing, retraction,
acknowledgment and moderation semantics remain to be defined.

### C. People key together

1. A person creates or joins a group using a deliberate sharing/invitation flow.
2. Members exchange the required secret or authenticated membership material.
3. A member encrypts a location-linked record for the group.
4. The service carries ciphertext; authorized clients decrypt and display it.
5. Membership changes, lost devices and expired invitations follow an explicit
   rekeying and recovery policy.

A shared secret is one candidate mechanism, not an already selected protocol.
Anyone holding a common symmetric key may be able to read and forge group
messages unless separate authentication is provided. Sharing a secret does not
by itself establish individual identity, revocation or forward secrecy.

### D. Follow the public plain-text feed

The public interface should work without the Android application or a complex
web frontend. It provides an initial location-filtered, time-ordered view and a
live continuation. The exact URL structure and streaming transport are undecided.

Specify before implementation:

- Geographic input format and query bounds.
- What timestamp defines order: author time, server acceptance time, or another
  value; tie-breaking and handling of clock skew and late-arriving records.
- Snapshot-to-live handoff, cursor semantics, reconnect and duplicate handling.
- Whether ascending chronological order is used for live delivery and whether
  snapshots can select an alternate order.
- A readable record format, encoding, boundaries, and escaping of multiline text,
  control characters and terminal escape sequences.
- Heartbeats, disconnects, backpressure, rate limits and maximum stream duration.
- Expiry checks at delivery time, including records waiting in an outbound queue.
- Cache/proxy behavior and whether expired records can be replayed from a cursor.

Research chunked text responses, Server-Sent Events and other suitable approaches.
Do not assume SSE's wire format is identical to an unframed `text/plain` feed.
The public plaintext feed must not silently decrypt or expose group content.
Whether ciphertext envelopes appear there, in a separate feed, or only through
an authenticated discovery route is an open decision.

## 4. Android application

Keep the initial client small and focused:

- Select a location manually or use permission-based device location.
- Browse and follow nearby/location-selected chats and alerts.
- Publish records with an explicit public/group choice and lifetime.
- Join/share a group through the selected key mechanism.
- Decrypt group content locally if end-to-end encryption is chosen.
- Display timestamps, location precision, expiry and connection status clearly.

These are candidate MVP capabilities, not a commitment to implement every feature
at once. Research Android foreground/background execution, location permissions,
battery use, reconnect behavior, notification delivery and secure key storage.
Do not assume an always-open stream is reliable while Android suspends an app.

Define offline storage and deletion behavior separately. Notifications, lock-screen
previews, clipboard use, screenshots, backups and crash logs can expose content or
keys even when the network payload is encrypted. Sharing mechanisms such as QR
codes or invitation links need explicit leakage and expiry rules.

## 5. Location privacy and public access

A location-based public service must not equate a query with consent to publish
someone's position. Minimize location collection and retention; make public
location precision and sharing choices explicit. Investigate whether coarse cells,
radii or named places meet the product need without collecting exact coordinates.

Research the consequences of:

- Query logs, IP addresses, timestamps and repeated requests revealing movement.
- Public message locations and group metadata enabling tracking or correlation.
- Guessable geographic regions enabling broad scraping and enumeration.
- Whether location is a filter, a claim, or an authorization condition. Supplying
  coordinates alone is not proof that a person is physically present.
- Public alerts being mistaken for verified emergency or official information.

Define a threat model covering observers, service operators, malicious publishers,
unauthorized readers, compromised or removed members, and lost client devices.
Choose sensible publishing limits, reporting/blocking and moderation boundaries.
Explain what moderation is possible when message bodies are encrypted.

## 6. Encryption and group research

Evaluate established protocols and maintained implementations rather than inventing
cryptography. Compare at least a shared-secret group model with an authenticated
group key-management approach, including whether Messaging Layer Security (MLS)
is suitable. No candidate is selected by this baseline.

For each candidate, document:

- Threat model, trust in the service, group discovery and metadata exposure.
- Key generation, exchange, invitation authentication and secure device storage.
- Content confidentiality, integrity, sender authentication and replay protection.
- Nonce/key reuse safety under multiple senders, retries and restored devices.
- Joining, leaving, removal, rotation, multiple devices and offline members.
- Whether newcomers can read history and removed members can read future data.
- Forward secrecy, post-compromise recovery, and limitations of each claim.
- Interoperability, Android support, library maturity, licensing and operational cost.

Do not put shared secrets in ordinary location-query URLs or public feed records.
Research routing identifiers that allow the service to locate relevant ciphertext
without treating the content decryption key as a public identifier.

## 7. Availability, expiry and deletion

“Only available or served for so long” is a core requirement, but its exact promise
needs operator selection. Separate these possible deadlines and guarantees:

| Concern | Decision needed |
| --- | --- |
| Serving lifetime | When new queries, streams and downloads stop returning the record. |
| Storage retention | When primary storage, replicas, indexes and backups remove it. |
| Group/invitation lifetime | When admission credentials or membership cease to work. |
| Key lifetime | Which keys rotate or are discarded and by whom. |
| Client retention | What the official client caches and when it removes cached content. |

Define who sets expiry, maximum/default lifetimes, authoritative clock, behavior
under skew, and whether expiry can be extended. Distinguish cleanup scheduling
from enforcement: a record still awaiting physical deletion need not remain
servable. Define enforcement across active streams, queued deliveries, reconnect
replay, replicas, proxies and offline clients.

**Limits:** a public plaintext recipient can retain a copy. A group member may
retain both ciphertext and its decryption key. Expiring an endpoint, rotating a
future key, or deleting the service's copy cannot force recipients to forget
already received data. Any narrower cryptographic expiry claim needs a stated
threat model and independently justified mechanism. Do not promise remote erasure.

## 8. Research deliverables and design gates

Before implementation, produce:

1. Operator decisions on public/group visibility and the intended expiry promise.
2. A location/query model and synthetic examples of chats and alerts.
3. A privacy/security threat model and encryption-candidate comparison.
4. A topology and end-to-end flow description spanning Android, plain-text clients,
   service APIs, storage and any evidenced OAG/mesh connection.
5. API contracts with request/response/stream examples, ordering, reconnect and errors.
6. Android feasibility notes and a deliberately bounded MVP plan.
7. Acceptance scenarios and unresolved limitations for review.

Keep research, specifications and future diagrams inside `oag-location-to-legacy/`.
Use existing OAG implementation evidence when investigating integration, but do not
copy assumptions from the developer-understanding project as established facts.
Record source versions and distinguish proposed mechanisms from implemented ones.

## 9. Initial acceptance scenarios to refine

- A location query returns matching unexpired records and excludes nonmatching ones
  according to the selected spatial rule.
- A plain-text client can read the initial public view and follow new records;
  reconnect behavior has defined ordering and duplicate/gap semantics.
- Public viewers never receive group plaintext through feeds or error messages.
- A valid group member decrypts a record; a nonmember cannot using only public data.
  Tampering and replay are handled according to the chosen protocol.
- Expired records are not newly served, including through stream queues and replay.
  Documentation acknowledges that prior recipients can retain copies.
- The Android client handles permission refusal, network loss, background suspension
  and expired records without silently exposing location or group secrets.
- Retention and revocation tests match the actual guarantees, not stronger promises.

## 10. Decisions for the next conversation

1. Does the service offer both fully public plaintext channels and encrypted group
   channels, or is encryption intended for all content except a public listing?
2. Does “share a key” mean one reusable group secret, a per-person invitation, or
   a user experience we should implement atop a stronger membership protocol?
3. Is expiry primarily a serving cutoff, storage deletion, group expiry, or a
   combination? What default and maximum lifetimes do you have in mind?
4. What makes records match a location: distance, geographic cells, named places,
   administrative areas, or something else?
5. What does “legacy” refer to, and what role should the existing OAG stack play?

Resolve these incrementally. They are not reasons to invent product decisions
or block all research while waiting for a complete design.

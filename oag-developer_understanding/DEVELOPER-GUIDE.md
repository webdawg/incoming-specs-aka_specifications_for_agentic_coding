# OAG developer guide — first research pass

Status: partial, source-inspected orientation, not a completed system specification.
Read [RESEARCH.md](RESEARCH.md) for exact revisions, evidence, coverage, and gaps.
No target tests or services were run. Statements below apply to the pinned inputs,
not automatically to later upstream changes.

## 1. Think of this as a family, not one deployable product

The six repositories do not define six copies of a graph server. The useful first
mental model is a core graph implementation, separate networking and computation
layers, a standalone audio service, a similarity library, and a proposed data
ownership/storage system. Integration must be established one boundary at a time.

| Repository | What a developer should initially understand | Evidence status |
| --- | --- | --- |
| open-agent-graph | A 15-member Rust workspace separates graph types, signing, storage, events, graph service, API/MCP, synchronization and additional capabilities. | Manifest inspected; one local commit path inspected (C-001, C-002). |
| oag-mesh | A separate crate reuses core types/signing/storage and exposes its own bridge, membership, corroboration, transport, dispatch and onion modules. | Dependencies and exports inspected, not all module implementations (C-003). |
| oag-imagination-random-data-compute | Routing/computation layer whose node adapter loads a pad and neighbor addresses from core storage. Its README frames generated data as synthetic, parallel to mesh rather than above it. | Adapter inspected; broader engine behavior remains to trace (C-004). |
| oag-listend | An audio-serving service with its own content-addressed storage surface and carrier modules. Not shown to depend on the core graph through Cargo. | Admission path and crate dependencies inspected (C-005). |
| oag-collisions | A local SimHash similarity index, not proof that two claims are true and not a graph database. | Tokenization, hashing and insertion inspected (C-006). |
| data-roads | A proposed ownership, access-agreement and custody model, not a runnable storage implementation in this repository. | Tracked-file inventory and full RFC inspected (C-007). |

### Dependency boundaries matter

Mesh and imagination both declare Git dependencies on core `main`, but their
lockfiles resolve **different core commits**, neither equal to this pass's core
checkout (C-008). Therefore “all repositories are on their latest branch” is not
a reproducible integrated configuration. Do not claim that a function inspected
in current core is exactly the function a locked mesh or imagination build uses.

Listend and collisions have no direct OAG crate dependencies in their inspected
crate manifests. That supports a narrow conclusion about build dependencies,
not a proof that no protocol-level or external integration exists.

## 2. Core: how a local event becomes committed state

The inspected `commit_local_event` routine handles a payload originating at this
peer. It is not the remote ingestion path (C-002).

1. Begin a SQLite transaction using `BEGIN IMMEDIATE`.
2. Read this identity's origin record within the transaction.
3. If an origin exists, derive the next sequence and previous-event ID from it;
   otherwise start at sequence 1 without a predecessor.
4. Build and sign the event using the local identity, sequence, predecessor,
   timestamp and payload.
5. Canonicalize the signed envelope and decode the signature bytes.
6. Insert the event into the event repository. An unexpected already-existing
   event returns an error instead of projecting the same local event twice.
7. Call the projector using the same transaction.
8. Advance the origin head and contiguous sequence, then commit the transaction.
9. Return the event ID and projection outcome only after the commit succeeds.

**Why this matters:** the intended persistence boundary includes the event log,
its projection, and its origin head. The implementation does not intentionally
commit these as three unrelated writes. An error before final commit prevents
this function from reporting success. Actual crash recovery, SQL constraints,
projector semantics, and storage configuration still need dedicated inspection
and tests; the routine alone does not establish every durability guarantee.

`GraphService::commit_event` acquires a mutex before calling this routine, based
on the inspected symbol search. The commit routine's comments warn about older
concurrent-write behavior while its body now uses `BEGIN IMMEDIATE`. Do not infer
a current reproduction of that warning from the comment alone. Trace lock scope,
multiple service instances, and regression tests next.

## 3. Imagination: what the node adapter actually loads

`NodeAdapter::load(database, node_peer_id)` opens core storage, acquires a
connection, fetches the supplied peer's tensor pad, and substitutes an empty
vector when no pad exists (C-004). It lists peers, excludes the supplied node's
own ID, and takes the first available address for each remaining peer. Peers
without addresses do not become neighbors.

It then constructs an engine with the hex-encoded node ID, that pad, collected
neighbors, and `SoftwareRandom`. This describes construction, not a verified
live-refresh mechanism: repeated neighbor/pad updates have not been traced.

The module's tests contain fixtures for pad/neighbor loading and missing pads,
but were only read, not executed. “Verifiable randomness” in the README is not
proof of a cryptographic beacon or reproducible computation. The actual random
source and verification contract need separate inspection.

**Operational caution:** this is an adapter without explicit pad/peer writes in
its body, not necessarily an OS-level read-only database connection. It invokes
`open_pool`; its own module commentary acknowledges embedded core migrations.
The current core also contains a migration call, but the adapter's exact locked
core implementation must be checked before stating its complete side effects.
Do not point a research process at a live database to test this casually.

## 4. Listend: identity is not content validity

An `AudioItem` hashes its bytes with BLAKE3. `verify` recomputes that hash and
compares it to the expected string. This establishes a byte-identity check, not
that the bytes are safe, meaningful, decodable, or authorized audio (C-005).

`AudioFormat::sniff` recognizes selected signatures/container markers. It is not
a full decoder. For example, its MP3 branch accepts an `ID3` prefix. A specification
must say **recognized signature**, not “proven valid audio stream.”

The inspected `AudioStore::accept` path:

1. Reject unrecognized bytes as `NotAudio`.
2. Reject bytes exceeding the computed servable bound as `TooLarge`.
3. Compute their content identity.
4. If already indexed, return that identity, attempting to restore a missing
   backing file from the existing item when directory storage is enabled.
5. For a new item in directory mode, await persistence before publishing it to
   the in-memory store. A persistence error propagates.
6. Publish the item and drain paths scheduled for eviction, then return its hash.

The inspected persistence helper creates directories and writes bytes using
Tokio filesystem calls; metadata sidecar failures are ignored. This pass has not
established fsync-level power-loss durability, atomic replacement, or carrier-wide
locking behavior. There are other ingestion/server paths to trace before treating
this method as an exhaustive description of every network request.

The README claims real Kubo integration has not been tested; do not upgrade stub
coverage into a real IPFS deployment claim. Current tests were not run here.

## 5. Collisions: similarity signal, not semantic truth

The basic tokenizer produces lowercase alphanumeric runs. Weighted SimHash hashes
each unit with BLAKE3, takes 64 bits, and accumulates signed per-bit votes. Positive
sums set output bits. Plain unigram aggregation is insensitive to token order:
the same multiset of words contributes the same sums regardless of sequence
(C-006). That is an algorithmic limitation, not a claim of semantic equivalence.

`insert_text` applies the index's configured tokenizer. Insertion removes any
existing item of that ID, computes the weighted hash, records insertion order,
and adds the ID to buckets for prefix lengths 1 through the configured level
count. Capacity enforcement removes oldest IDs; a configured capacity of zero
bypasses that bound. Default capacity is 100,000 and default prefix count is 64.

At 64 levels, each item contributes 64 bucket memberships. Actual memory includes
maps, vectors and cloned string IDs; do not report “64 small integers per item”
as an accurate memory model. Replacing an existing ID also moves it to the new
insertion position, which matters for subsequent eviction.

**Concrete documentation discrepancy:** the constructor comment says one level
indexes only exact-hash matches. Insertion actually uses `hash >> (64 - length)`;
at length one that is only the most significant bit, not all 64 bits. Query and
ranking logic must be traced before specifying end-to-end behavior, but the
insertion-side discrepancy is already evident.

The README discusses Hamming re-ranking and multi-probe recall experiments.
Those reported performance numbers have not been reproduced or accepted as
current independent measurements in this guide.

## 6. Data Roads: requirements without an implementation shortcut

The RFC distinguishes an **owner** (generator by default), a **custodian**
(stores or relays data), and an **access agreement** (bounded renewable access).
Custodian compensation is separate from what a requester provides to an owner
(C-007). These distinctions prevent “holds a copy” from silently becoming “owns
or may disclose the content.”

The RFC proposes encryption, distribution and renewable access, but leaves the
actual redundancy model, payments, renewal mechanics and event/interface mapping
unresolved. Its security section is explicitly unfinished. It also recognizes
that revocation or key rotation cannot erase plaintext already disclosed.

No runtime implementation is present in this repository's four tracked files.
Do not turn that into a global claim that related repositories implement none of
its ideas. Conversely, a core event log or mesh transport does not automatically
implement ownership-gated retrieval, effective redundancy or enforceable expiry.

## 7. What to read next

Next pass should follow one real core operation from an API handler into the
graph service, event builder, projector and SQL schema, then compare remote
ingestion and replay. Produce an example with exact request/response and state
changes, including failure behavior. After that, investigate versioned integration
boundaries with mesh and imagination before describing a unified deployment.

See [RESEARCH.md](RESEARCH.md) for the prioritized questions and continuation state.

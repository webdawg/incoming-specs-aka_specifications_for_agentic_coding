# Research ledger and continuation state

## Status

Pass 1: repository discovery and selective source inspection complete; full
analysis **not complete**. No target code changed. No target builds, tests,
services, migrations, or integration experiments executed. No independent review.
Documentation checks are not target-system validation.

Research checkouts are disposable and outside this publication repository. They
are not deliverables and need not survive the session. Recover research from the
public repositories and exact revisions below, not from a machine-specific path.

## Pinned inputs

| Repository (webdawg/) | Branch observed | Commit inspected |
| --- | --- | --- |
| open-agent-graph | main | `c41e37dad90ae9d920c94a3b01a1698e823beb5c` |
| oag-mesh | main | `fe51e4fbed6f9a0d28a07644d7a44ef5543d63f0` |
| oag-listend | master | `2480f5fc98982ee18cc787af26432e06b5ccdf37` |
| oag-collisions | master | `dfc8ca95ce9641c29dec6bebe1586936b113c883` |
| oag-imagination-random-data-compute | master | `7b7dcb8eae70b2df2fa1329fdb7b2dd928e76d29` |
| data-roads | main | `b5cd2f4e8618f955eb06dd1c4d3edc7234041a7e` |

Git history was shallow-cloned for this pass; historical intent and change
provenance were not investigated. No `AGENTS.md` or `CLAUDE.md` files were found
in these research checkouts during discovery. Other technical documents may
contain applicable guidance and must be read before target execution.

## Evidence ledger

Claim IDs are referenced in [DEVELOPER-GUIDE.md](DEVELOPER-GUIDE.md).
All runtime claims below are static inspection or documented intent, not newly
observed runtime behavior.

### C-001 — core workspace structure

Basis: full [Cargo.toml](https://github.com/webdawg/open-agent-graph/blob/c41e37dad90ae9d920c94a3b01a1698e823beb5c/Cargo.toml)
and source-path inventory. Fifteen workspace members are listed. The separate
`oag-brain/Cargo.toml` was discovered but not read; it is not in that member list.
Limit: module names do not prove capability completeness or deployment topology.

### C-002 — local event transaction

Basis: full [commit.rs](https://github.com/webdawg/open-agent-graph/blob/c41e37dad90ae9d920c94a3b01a1698e823beb5c/crates/oag-events/src/commit.rs),
`commit_local_event`. The routine begins an immediate transaction, reads origin
state, signs/inserts, projects, advances the head and commits. Symbol search in
[service.rs](https://github.com/webdawg/open-agent-graph/blob/c41e37dad90ae9d920c94a3b01a1698e823beb5c/crates/oag-graph/src/service.rs)
found a mutex acquisition in `commit_event` (around lines 160–165).
Limit: caller chain, SQL definitions, projection internals, remote ingestion,
transaction rollback implementation and crash tests have not yet been read fully.
The concurrency warning in the comment must be reconciled with current
`BEGIN IMMEDIATE` behavior; no concurrency defect was reproduced.

### C-003 — mesh boundary

Basis: full [Cargo.toml](https://github.com/webdawg/oag-mesh/blob/fe51e4fbed6f9a0d28a07644d7a44ef5543d63f0/Cargo.toml)
and [lib.rs](https://github.com/webdawg/oag-mesh/blob/fe51e4fbed6f9a0d28a07644d7a44ef5543d63f0/crates/oag-mesh/src/lib.rs).
The manifest declares core/crypto/storage dependencies; the library exports
bridge, membership, corroboration, transport, dispatch, onion and other surfaces.
Limit: exported names and module comments are not verified protocol semantics.
Transport, persistence, authorization, rate limits and threat model remain unread.

### C-004 — imagination adapter

Basis: full [node.rs](https://github.com/webdawg/oag-imagination-random-data-compute/blob/7b7dcb8eae70b2df2fa1329fdb7b2dd928e76d29/crates/oag-imagination/src/node.rs)
including embedded tests, and full
[README](https://github.com/webdawg/oag-imagination-random-data-compute/blob/7b7dcb8eae70b2df2fa1329fdb7b2dd928e76d29/README.md).
`NodeAdapter::load` reads pad/peer/address data and constructs an engine using
`SoftwareRandom`. Tests were inspected, not executed.
Limit: engine, random source, transports and exact locked storage implementation
remain unexamined. The migration caution is supported by adapter commentary and
its `open_pool` call; a migration invocation was also found by symbol search in
current core's [pool.rs](https://github.com/webdawg/open-agent-graph/blob/c41e37dad90ae9d920c94a3b01a1698e823beb5c/crates/oag-storage/src/pool.rs),
not a full review of the adapter's locked core revision.

### C-005 — audio identity and admission

Basis: [lib.rs](https://github.com/webdawg/oag-listend/blob/2480f5fc98982ee18cc787af26432e06b5ccdf37/crates/oag-listend/src/lib.rs)
lines 1–240 and 480–594: `AudioItem`, `AudioFormat::sniff`,
`AudioStore::accept`, `persist`; full crate
[Cargo.toml](https://github.com/webdawg/oag-listend/blob/2480f5fc98982ee18cc787af26432e06b5ccdf37/crates/oag-listend/Cargo.toml);
[README](https://github.com/webdawg/oag-listend/blob/2480f5fc98982ee18cc787af26432e06b5ccdf37/README.md)
read through line 180. Sniffing is explicitly not full decoding. The manifest
lists blake3, thiserror and tokio, not OAG crates.
Limit: helper/caller paths, network parsing, eviction and mirroring coverage are
partial. Async filesystem calls do not alone establish lock-free ingestion or
power-loss durability. README test counts and manual-check claims are upstream
reports, not results reproduced here.

### C-006 — collision index

Basis: [lib.rs](https://github.com/webdawg/oag-collisions/blob/dfc8ca95ce9641c29dec6bebe1586936b113c883/crates/oag-collisions/src/lib.rs)
lines 1–240 and 300–559: tokenizer variants, weighted SimHash, constructors,
insertion and capacity enforcement; full
[README](https://github.com/webdawg/oag-collisions/blob/dfc8ca95ce9641c29dec6bebe1586936b113c883/README.md)
and crate [Cargo.toml](https://github.com/webdawg/oag-collisions/blob/dfc8ca95ce9641c29dec6bebe1586936b113c883/crates/oag-collisions/Cargo.toml).
The one-level exact-match comment conflicts with the insertion shift. Bucket
members are `String` IDs, not merely small integers. Default unigram permutation
invariance follows from commutative weighted accumulation.
Limit: query/ranking, removal internals and most tests not yet read. Experiments
and their statistics were not reproduced. No end-to-end correctness verdict.

### C-007 — Data Roads proposal

Basis: full [README](https://github.com/webdawg/data-roads/blob/b5cd2f4e8618f955eb06dd1c4d3edc7234041a7e/README.md),
full [RFC](https://github.com/webdawg/data-roads/blob/b5cd2f4e8618f955eb06dd1c4d3edc7234041a7e/rfc/0001-data-roads.md),
and `git ls-files` inventory: LICENSE, README, RFC and companion paper only.
Classification: documented intent, not executable implementation. The companion
paper has not been read. The RFC's description of mesh transport must be compared
with current mesh source rather than assumed current.

### C-008 — integration revision skew

Basis: searches of the mesh and imagination manifests and lockfiles:

- [mesh Cargo.lock](https://github.com/webdawg/oag-mesh/blob/fe51e4fbed6f9a0d28a07644d7a44ef5543d63f0/Cargo.lock)
  resolves core Git packages to `59ccf3de95d211f9acaf1766d2083335bac32ccf`.
- [imagination Cargo.lock](https://github.com/webdawg/oag-imagination-random-data-compute/blob/7b7dcb8eae70b2df2fa1329fdb7b2dd928e76d29/Cargo.lock)
  resolves core Git packages to `4bdef2d00b706b9d210b9f2b1678978f94a2b4f1`.

Neither equals the core checkout pinned above. This establishes different locked
inputs, not incompatibility. The older revisions were not fetched or compared.

## Coverage and excluded material

This is a family-wide orientation plus selected mechanism traces. It is **not** a
recursive full read of every repository. The source ranges above record exactly
where inspection stopped. Inventory/search results are weaker evidence than full
function and caller inspection.

Not reviewed: most core modules and existing specs; mesh implementations;
imagination engine/transports/random sources; audio carrier implementations;
collision query and experiment implementations; Data Roads companion paper.
Generated artifacts, raw session-like logs, source credentials and operational
state are not publication inputs. No target source was vendored here.

## Open questions and next passes

1. **Core vertical slice:** choose one node/assertion operation. Read its handler,
   authorization, graph service, signing, projector, SQL and relevant tests. Write
   a request-to-state-change walkthrough with concrete synthetic inputs.
2. **Local versus replicated events:** trace verification, ordering, duplicate and
   gap handling, rejection, and replay. What is shared with the local commit path?
3. **Concurrency:** reconcile the local-event warning with `BEGIN IMMEDIATE` and
   the graph-service mutex. Identify the scope of one writer and multiple instances.
4. **Versioned integration:** inspect core at both locked revisions before
   specifying mesh/imagination storage or signature compatibility.
5. **Audio admission paths:** compare `accept` with server-side helpers and every
   carrier. Resolve lock scope and persistence guarantees from actual callers.
6. **Similarity:** trace query thresholds, prefix-level handling, Hamming ordering,
   multi-probe budgets and eviction/removal before proposing corrected prose.
7. **Synthetic computation:** what does `SoftwareRandom` provide, what is recorded,
   and what can another peer actually reproduce or verify?
8. **Mesh versus Data Roads:** separate implemented transport/persistence from
   proposed durable custody, access agreements, ownership and compensation.

Suggested next deliverable:
`specs/webdawg/open-agent-graph/` with a substantive first vertical-slice spec,
evidence, coverage and unanswered questions. Keep all such outputs inside this
OAG folder. No need to create empty six-repository boilerplate now.

## Changes to the methodology motivated by this pass

- Record resolved dependency revisions as well as repository HEADs.
- Distinguish library capability, instantiated runtime path and deployed behavior.
- Treat comments as claims: record precise comment/body discrepancies.
- Separate signature sniffing from full data validation and async I/O from durability.
- For RFC-only targets, document requirements and missing mechanisms; do not invent
  an implementation to force a runtime-oriented template to fit.

## Review

Self-review only. This guide can explain the selected mechanisms and family
boundaries, but cannot yet answer the complete operational, authorization,
concurrency or cross-repository integration questions in the baseline acceptance
checklist. Acceptance as a comprehensive developer specification is pending.

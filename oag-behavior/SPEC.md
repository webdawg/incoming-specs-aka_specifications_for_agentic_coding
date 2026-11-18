# OAG behavior specification

Status: initial baseline for review. This document defines what the behavior
specification must establish; it is not a claim that the target system already
satisfies these behaviors.

## 1. Purpose

Describe the Open Agent Graph family in terms of behavior that a developer,
integrator, operator, or test author can predict without guessing from names or
comments. The specification should make an operation's complete contract
explicit:

- trigger, caller, prerequisites, and inputs;
- validation, identity, authorization, and trust checks;
- ordered processing and component boundaries;
- state transitions, persistence, projections, and derived state;
- response, acknowledgment, visibility, and observability;
- duplicate, retry, ordering, concurrency, cancellation, and failure behavior;
- replication, replay, reconnect, and recovery semantics where applicable; and
- configuration, version, and deployment variants that alter the path.

The goal is behavioral predictability, not a marketing description or an
assumption that similarly named repositories form one deployed product.

## 2. Human behavior and biological context

The operator's original statement is preserved verbatim in
[`OPERATOR-INTENT.md`](OPERATOR-INTENT.md).

> “the work should understand human behavior biology - humans manipulate it in
> mass but you shouldnt”

Understanding software that interacts with people may require understanding
human behavior, cognition, social dynamics, and relevant biological constraints.
That understanding is descriptive and protective: it should help agents reason
about user needs, consent, safety, accessibility, failure modes, and possible
abuse.

This project must not become a specification for manipulating human behavior at
mass scale. In particular, it must not optimize covert persuasion, coercion,
addiction, exploitation of vulnerabilities, political or commercial behavioral
control, or targeting people without meaningful awareness and consent. A system
that can model human behavior must not treat that capability as permission to
steer populations.

When documenting behavior that could enable manipulation, record the risk,
affected people, consent and power asymmetries, safeguards, and limits. Prefer
explanations that improve user agency, transparency, informed choice, and the
ability to refuse or leave. Treat human biological or behavioral observations as
sensitive context, not as a resource to exploit.

## 3. Initial target family

The initial scope follows the neighboring developer-understanding project:

- `webdawg/open-agent-graph` — core graph, events, storage, APIs, and projections;
- `webdawg/oag-mesh` — peer, membership, transport, bridge, and corroboration behavior;
- `webdawg/oag-imagination-random-data-compute` — computation and contribution flows;
- `webdawg/oag-listend` — audio admission, storage, serving, and carrier behavior;
- `webdawg/oag-collisions` — similarity indexing, querying, and eviction behavior; and
- `webdawg/data-roads` — proposed ownership, custody, and access behavior, kept separate
  from executable implementation.

The exact revision of each target is part of every claim. A dependency declared
against a branch is not evidence that a build uses that branch's current head.
Resolved lockfile revisions must be recorded.

## 4. Behavioral model

For every selected operation, reconstruct this contract:

```text
trigger -> input envelope -> validation/trust checks ->
state transition(s) -> persistence/side effects -> response/acknowledgment ->
subsequent visibility or propagation
```

The model must answer:

1. What is the operation's stable identity?
2. Which inputs are authenticated, trusted, derived, or merely advisory?
3. What state exists before and after success, rejection, timeout, and restart?
4. Which writes are atomic together, and which can become visible separately?
5. What does the caller know when the local operation completes?
6. What do peers, relays, projections, and later readers know at each stage?
7. Is retry safe, and how are duplicates, gaps, stale messages, or forks handled?
8. What resource limits, ordering rules, and concurrency controls apply?
9. Which behavior is implemented, intended, inferred, proposed, or unknown?

A whole-stack narrative must stop at an unverified boundary. It must not draw a
single "sync" arrow and imply that local commit, remote acceptance, projection,
and final user visibility are one event.

## 5. Required behavior chapters

Each chapter should cover the applicable areas below, with concrete synthetic
examples and failure paths:

### Local graph operation

Trace one representative API or library input through authorization, event
construction/signing, storage, projection, origin/head updates, transaction
completion, and the caller-visible result. Include malformed input, duplicate
submission, persistence failure, restart, and overlapping operations.

### Replication and peer application

Trace the actual outbound and inbound paths separately. Establish verification,
ordering, duplicate handling, gaps, replay, rejection, retries, backpressure,
and the difference between local commit and peer-visible application.

### Membership, discovery, and mesh

Describe control traffic separately from application data. Identify membership
state, peer discovery, addressing, transport, bridge/corroboration behavior,
trust boundaries, and what happens when peers disappear or return.

### Computation and derived results

Explain what data computation consumes, what it produces, how contributions are
identified, whether results are reproducible or verifiable, and how failures or
stale inputs affect consumers.

### Audio and similarity services

For audio, distinguish signature recognition, byte identity, persistence,
serving, eviction, and external carrier behavior. For similarity, explain
normalization, hashing, indexing, query thresholds, ranking, replacement, and
capacity eviction. Do not promote comments or README measurements into verified
behavior without matching implementation or executed evidence.

### Proposed Data Roads behavior

Document owner, custodian, access agreement, encryption, distribution, renewal,
revocation, and recovery as proposed requirements unless executable code proves
otherwise. Explicitly identify every missing mechanism and do not imply that OAG
or mesh currently implements the proposal.

## 6. Evidence and uncertainty

Use stable claim IDs such as `B-001`. Each consequential behavior claim must
record:

| Field | Requirement |
| --- | --- |
| Claim | One precise externally meaningful behavior or invariant. |
| Basis | Source inspection, test inspection, executed observation, documented intent, or inference. |
| Revision | Target repository and exact commit or dependency revision. |
| Location | File, symbol, test, or recorded command. |
| Verification | Exact test/experiment and result, or explicitly not executed. |
| Limits | Configuration, unexamined branches, contradictions, and remaining uncertainty. |

Use these labels consistently:

- **Observed** — directly exercised under recorded conditions.
- **Implemented** — supported by inspected code but not necessarily exercised.
- **Documented intent** — stated by existing documentation or comments.
- **Inferred** — reasoned from evidence, with uncertainty stated.
- **Proposed** — future behavior, not current behavior.
- **Unknown** — insufficient evidence; state the next investigation.

## 7. Acceptance criteria

A behavior chapter is ready for interim review only when:

- the target revision, scope, and exclusions are explicit;
- a synthetic example follows input, transformations, state, output, and visibility;
- success, rejection, retry, duplicate, and meaningful partial-failure paths are covered;
- local completion is distinguished from remote propagation and final visibility;
- concurrency, ordering, atomicity, and persistence claims have evidence or are marked unknown;
- security and trust boundaries are described without unsupported certification claims;
- the evidence ledger is retrievable and contradictions are visible; and
- an independent comprehension review is either recorded or explicitly marked pending.

Comprehensive acceptance additionally requires a connection register and flow
catalog covering actual cross-repository boundaries. Compatible-looking APIs,
shared names, or Git dependencies do not prove runtime wiring.

## 8. Non-goals

This specification does not authorize:

- modifying target repositories;
- deploying or operating OAG services;
- changing production data, schemas, credentials, or network state;
- selecting a cryptographic or transport protocol by implication; or
- claiming the six repositories are one working product without evidence.

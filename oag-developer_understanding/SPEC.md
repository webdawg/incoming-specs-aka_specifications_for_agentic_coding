# OAG developer understanding — reverse-built software specifications

Status: initial baseline, proposed for review. This is a specification for the
analysis process and its deliverables, not an implementation or a completed
analysis of any target repository.

License: AGPL-3.0-or-later.

## Foundational requirement: rapid, open debugging access

The OAG system must be designed and analyzed with rapid debugging access as a
permanent, always-available capability—not as an emergency feature to add later.
An authorized developer or operator must be able to inspect the state of the
entire system immediately, including the relevant nodes, processes, event and
projection state, queues, peer/membership state, configuration, errors, and
cross-repository flow status. A complete state dump must be available on demand,
without requiring a special rebuild, a fragile reproduction, or a prolonged
investigation first.

This requirement applies regardless of the system's current mode or failure
state. The analysis must document the exact dump/debug entry points, their
coverage, consistency and timestamp semantics, access controls, performance
cost, sensitive-data handling, behavior during partial failure, and how a dump
can be correlated across nodes and repositories. “The system logs something” is
not sufficient; the goal is immediate, structured, whole-system inspectability.

The capability must remain open and useful to authorized debugging—not hidden
behind assumptions that only the original implementer can reconstruct. It must
also be explicit about what is unavailable when a component is dead, partitioned,
corrupted, or unreachable, rather than claiming an impossible omniscient dump.

## 1. Purpose

Software developers need to understand software deeply enough to reason about,
change, operate, and trust it. Agent-generated code can accumulate faster than
a human can reconstruct its design from individual files. This project defines
how an agent should reverse-build an expert-level specification from an existing
codebase and publish that explanation here.

The goal is **understanding the software without having to read its source**.
The analyzing agent must examine the source; the reader should not need to.
Source references are an audit trail, not a substitute for an explanation.

The original request is preserved in [OPERATOR-INTENT.md](OPERATOR-INTENT.md).
Everything below is an initial interpretation, open to revision through actual
use. No particular architecture, programming language, or target project is
assumed.

## Analysis targets

This specification covers the following public GitHub repositories:

| Repository | Initial orientation (verify against implementation) |
| --- | --- |
| [webdawg/open-agent-graph](https://github.com/webdawg/open-agent-graph) | Core semantic graph for AI agents. |
| [webdawg/oag-mesh](https://github.com/webdawg/oag-mesh) | Cluster bridging and federated corroboration. |
| [webdawg/oag-listend](https://github.com/webdawg/oag-listend) | Audio-serving daemon. |
| [webdawg/oag-collisions](https://github.com/webdawg/oag-collisions) | Role to establish through analysis; no repository description was available. |
| [webdawg/oag-imagination-random-data-compute](https://github.com/webdawg/oag-imagination-random-data-compute) | Described as “helps ai dream”; mechanisms to establish through analysis. |
| [webdawg/data-roads](https://github.com/webdawg/data-roads) | RFC/paper built on OAG and oag-mesh; distinguish proposals from executable implementation. |

Analyze each at a recorded commit and explain evidenced relationships across the
set. Shared naming is not proof of implemented integration. Selecting these
repositories authorizes the documentation scope, not deployments or live changes.
All documents and outputs belonging to this specification stay within
`oag-developer_understanding/` in the containing multi-spec repository.

## 2. What success means

A developer reading the result can explain:

- What the software does, for whom, and where its responsibilities end.
- How its components collaborate and why their boundaries matter.
- How data moves end to end across the entire node/cluster stack, including
  cross-repository connections, return paths, and partial failures.
- What happens, step by step, during its important operations.
- What data means, where it lives, and how it changes over time.
- Which contracts and invariants must remain true.
- How failures, retries, concurrency, security, and recovery actually work.
- How to build, test, configure, run, debug, and safely change the software.
- What is implemented, incomplete, unverified, or unknown.

This is not a marketing README, a symbol inventory, a paraphrase of comments,
or an imagined ideal design. Detail must explain mechanisms and consequences.
Length alone is not evidence of completeness.

The result cannot guarantee that arbitrary code changes are safe without
consulting current source and tests. It should supply the mental model needed
to make that consultation focused rather than exploratory.

## 3. Blind-entry analysis contract

“Go in blind” means start without presumed knowledge of the target's design,
previous agent conversations, or undocumented author intent. It does not mean
ignore repository instructions, permissions, security, or existing evidence.

Before analyzing, record:

1. Authorized target repository or repositories and publication destination.
2. Exact commit SHA for every target, branch, and working-tree status. Also record
   resolved cross-repository dependency revisions from lockfiles; a dependency on
   a branch does not mean the locked build uses that branch's current HEAD.
3. Scope: included subsystems, excluded content, and external dependencies.
4. Applicable repository instructions and any approved execution permissions.
5. Available tools and limitations, including unavailable services or hardware.

Treat existing specs, READMEs, comments, issues, and tests as claims to compare
against implementation, not as unquestionable ground truth. Preserve conflicts
rather than silently choosing whichever narrative is easiest to repeat.

Do not modify target code during analysis. Do not run deployments, migrations,
production workloads, or untrusted setup scripts merely to discover behavior.
Use approved isolated execution where appropriate; report when execution is not
authorized or feasible. Never publish secrets, private configuration, personal
data, or proprietary source into this public repository. Public source alone
does not make every embedded value safe to repeat.

### Execution and sensitive findings

Before running target builds or tests, record the approved execution scope and
isolation mechanism. Builds can execute code through build scripts, procedural
macros, dependency tooling and hooks, not just the final application. Use a
disposable environment with no production mounts or credentials; constrain and
record network access and writable paths. Separate dependency retrieval from
restricted execution where practical. If adequate isolation is unavailable,
continue static analysis and report the limitation rather than quietly running
against the host environment.

If sensitive material is discovered, do not reproduce it in public notes, tool
output or commits. Pause the affected publication, notify the operator through
an appropriate private channel, and agree on handling. Do not rotate credentials,
erase history or change target systems without separate authorization. Unrelated
safe analysis may continue; document only a sanitized coverage limitation when
needed, without exposing the sensitive value or identifying detail.

## 4. Method: reconstruct before summarizing

### Phase A — discovery and coverage plan

Inventory entry points, packages, generated/vendor code, executable scripts,
build definitions, dependency manifests, test suites, schemas, configuration,
deployment assets, and existing documentation. Record what will be examined and
why. Explicitly mark omitted areas and their implications.

Identify actual executable paths, not just promising filenames. Determine which
flags, build features, deployment modes, and versions change those paths.

Maintain a prioritized coverage list of candidate operations and flows. Rank them
by developer relevance, architectural importance, failure consequences and how
many important boundaries they illuminate. Record selected, deferred and excluded
items with short reasons. Start with a concrete high-value flow, not an arbitrary
file order; revisit priorities as source inspection reveals new dependencies.
Coverage is an explicit inventory, not a claim that representative samples cover
every behavior.

### Phase B — reconstruct the system model

Trace from public entry points through internal behavior to storage, network,
process, or user-visible effects. Build a model of components, contracts, state,
and dependency direction. Read important implementations and their callers;
function names and comments alone are insufficient.

Explain boundaries and tradeoffs. If the rationale is not evidenced, label it
as an inference or leave it unknown. Never attribute an invented intention to
the author.

### Phase C — deep behavioral analysis

For each important operation, describe:

- Trigger, inputs, validation, authorization, and prerequisites.
- Ordered processing steps and component interactions.
- State transitions, persistence, and externally visible side effects.
- Completion conditions and outputs.
- Error handling, partial completion, cancellation, timeout, retry, and recovery.
- Concurrency, ordering, atomicity, deduplication, and idempotency where relevant.
- Variants controlled by configuration or optional features.

Include worked examples with safe synthetic data. Trace unhappy paths as
carefully as successful ones. Do not invent guarantees from the absence of an
obvious counterexample.

### Phase D — verification and reconciliation

Compare the model against code, tests, and documentation. When authorized, run
relevant tests or isolated experiments and record exact commands, environment,
results, and limits. Test names are not proof that their claimed behavior was
executed successfully. Passing unit tests are not proof of production behavior.

Investigate contradictions, including comments that no longer match their function
bodies. Separate library capability, actual caller paths, and deployed behavior.
Do not equate signature sniffing with full validation or asynchronous writes with
power-loss durability. For RFC-only targets, explain proposed contracts and missing
mechanisms rather than inventing runnable behavior.

Document the observed implementation separately
from desired behavior and proposed fixes. Identify dead code, unreachable
features, placeholder implementations, and untested branches as findings only
when evidence supports that classification.

### Phase E — write and challenge the specification

Write from the developer's questions outward, not in directory traversal order.
Use an overview followed by progressive detail. Have a reader or independent
reviewer attempt the comprehension checks in section 9 using only the resulting
documents. Record unanswered questions and revise the analysis accordingly.

If no independent reviewer is available, label the check as self-review; do not
claim independent validation. Publish bounded partial results rather than
pretending gaps are complete. Final acceptance requires a separate reviewer—an
operator or another agent/session—answering the comprehension questions from the
published documentation alone, without source or unpublished authoring notes.
Record answers and unresolved failures. This independent understanding check is
separate from source-based factual verification; both matter.

## 5. Required content of a target specification

Every area below must be covered or explicitly marked not applicable with a
reason. Small projects may combine documents; large projects should split them.

| Area | Required detail |
| --- | --- |
| Purpose and scope | Users, use cases, terminology, responsibilities, non-goals, system boundary. |
| Architecture | Components, ownership, deployment/process topology, dependency direction, trust boundaries, diagrams with explanatory prose. |
| Code organization | Important modules and entry points, their responsibilities and relationships; not an exhaustive unannotated file list. |
| Behavior | End-to-end workflows, algorithms, state machines, edge cases, observable outcomes. |
| Data model | Entities, field meaning, identifiers, relationships, validation, invariants, schemas, serialization, migrations, lifecycle and deletion. |
| Interfaces | API/CLI/event/library contracts, inputs, defaults, outputs, errors, compatibility, worked requests and responses. |
| Persistence and distribution | Transaction boundaries, durability, consistency, replication, ordering, conflict handling, reconnect and replay semantics where applicable. |
| Concurrency | Shared state, synchronization, ownership, scheduling, backpressure, races and resource limits. |
| Security | Authentication, authorization, trust assumptions, input handling, secret handling, abuse cases and limitations; not an unsupported security certification. |
| Configuration | Names, meaning, defaults, precedence, validation, reload behavior, effects and safe examples. |
| Operations | Build/run requirements, dependencies, deployment, logs/metrics, health, diagnostics, backup/recovery, shutdown and upgrades. |
| Performance | Important costs, bottlenecks and resource behavior; separate measured results from estimates and untested scaling claims. |
| Testing | Test layers, what they establish, how to run them safely, results actually obtained, important gaps. |
| Change impact | Extension points, coupling, contracts likely affected by common changes, migration and compatibility concerns. |
| Limitations | Known defects, unfinished work, contradictions, unknowns and unsupported modes. |

Explain algorithms and domain decisions at the level needed to predict outcomes.
Use pseudocode, tables, diagrams, and examples when they clarify behavior. Do not
replace the specification with copied source or require readers to follow links
to understand a critical mechanism.

### Whole-stack connections and end-to-end data flows

The developer manual must explain the connected system, not merely assemble six
independent repository descriptions. A reader must be able to follow a datum from
its producer through every relevant layer, node and cluster to its final consumer,
and understand the response, acknowledgment and recovery paths without opening
source. Not every operation traverses every repository: document the actual path,
not an invented pipeline that forces all six projects into one sequence.

Required system-level artifacts:

1. **Topology and connection map.** Distinguish repositories, libraries, processes,
   nodes, clusters, external services and persistent stores. Show what runs where,
   who initiates each connection, discovery/configuration prerequisites, transport,
   protocol and compatibility requirements. Separate build dependencies, shared
   database access and runtime network connections. Define node/peer/cluster terms
   from implementation rather than treating them as interchangeable.
2. **Connection register.** Give each significant boundary a stable ID and record
   both endpoints, their pinned revisions, the actual caller and receiver, payload
   contract, trust boundary and evidence. Label connections as observed,
   implementation-supported, proposed, unknown or unsupported by the inspected
   path. Existence of two compatible-looking APIs is not proof they are wired up.
3. **Flow catalog and sequence diagrams.** Give each important user-visible or
   background flow a stable ID, trigger, prerequisites, participants and terminal
   result. Pair every diagram with a complete prose walkthrough; an arrow labeled
   “sync” or “process” is not a sufficient explanation.
4. **Worked data lineage.** Carry a safe synthetic example through every hop.
   Show identifiers and correlation, payload/schema changes, serialization,
   signatures/hashes, encryption boundaries, copies, queues, caches, persistence,
   projections and derived results. Explain what stays identical and what changes.
5. **Completion and failure semantics.** Identify where work is accepted, committed,
   acknowledged, replicated, applied and visible. Do not equate local success with
   cluster-wide propagation or agreement. Explain timeout, retry, duplicate and
   out-of-order delivery, backpressure, partitions, reconnect, replay and partial
   completion. State how the caller learns the outcome—or why it cannot know.
6. **Trust and observability along the path.** Explain identity, authorization and
   validation at each boundary, what each recipient trusts, and what logs, metrics
   or identifiers can trace the operation. Distinguish existing instrumentation
   from instrumentation we would like to have. Keep real deployment secrets out.

Investigate these flow families, documenting actual coverage or explicit gaps:

- Client/agent input through a local node to persistent graph state and response.
- Peer-to-peer propagation within a cluster, remote application and later reads.
- Cross-cluster bridge/corroboration exchanges and their return paths.
- Discovery, membership, routing and synchronization control traffic, distinguished
  from application data rather than hidden inside a generic network arrow.
- Imagination routing and contribution/result return; collision analysis inputs
  and outputs; audio ingestion, retrieval and optional external distribution.
- Proposed Data Roads ownership/access/custody flows, kept visibly separate from
  implemented behavior and blocked at any missing integration mechanism.

For every applicable flow, follow both the successful path and meaningful failure
variants. If a boundary is not implemented or cannot be established, stop the
implemented trace there, explain the gap, and show any proposed continuation as
proposed. Preserve version differences and optional deployment modes explicitly.
This is a requirement for further research, not a claim these flows are already
verified or that all layers form one working deployment.

## 6. Evidence and uncertainty

Assign stable claim identifiers to consequential claims, especially guarantees,
security properties, persistence behavior, and externally visible contracts.
Prefer one claim per meaningful externally observable behavior, contract, or
security/durability property, rather than per function or sentence. Split claims
when their evidence, revision scope or uncertainty differs materially. Keep
supporting bookkeeping subordinate to the explanation.

Maintain an evidence ledger with these fields:

| Field | Meaning |
| --- | --- |
| Claim ID | Stable reference from the narrative, such as `C-001`. |
| Statement | The precise behavior or property being claimed. |
| Basis | Implementation inspection, test inspection, executed observation, documented intent, or inference. |
| Source | Repository, pinned commit, path, symbol and line range where useful. |
| Verification | Command/test and result if executed; otherwise explicitly not executed. |
| Limits | Configuration, environment, unexamined cases, contradictory evidence. |

Keep these distinctions explicit:

- **Observed:** directly exercised under recorded conditions.
- **Implemented:** supported by inspected code but not necessarily exercised.
- **Documented intent:** stated by existing docs or comments; implementation may differ.
- **Inferred:** reasoned interpretation with its basis and uncertainty stated.
- **Unknown:** insufficient evidence; include the next question or investigation.
- **Proposed:** a future improvement, never described as current behavior.

An evidence tag is not a correctness guarantee. A claim can have several kinds
of supporting evidence and still have limits. Recheck citations against the
pinned revision before publication. Do not imply full coverage from sampling.

For each consequential contradiction, record the conflicting claims, evidence,
reader impact, resolution status, and next action or responsible role. Distinguish
further source investigation from an operator decision about desired behavior.
A decision about intent does not change observed implementation. Explicitly
justify any deferred or unresolved discrepancy rather than silently parking it.

## 7. Deliverables and repository layout

Proposed layout for analyses written to this repository:

```text
oag-developer_understanding/specs/<owner>/<repository>/
  README.md             # navigation, target revision, scope and reading order
  SPEC.md               # coherent system specification and detailed explanations
  EVIDENCE.md           # claim ledger and verification results
  COVERAGE.md           # examined areas, exclusions, remaining work
  OPEN-QUESTIONS.md     # contradictions, unknowns and next investigations
  REVIEW.md             # comprehension checks, reviewer and acceptance status
```

Maintain whole-stack documentation in `oag-developer_understanding/flows/` as
substantive research is produced: an index/topology, a connection register, and
individual flow narratives with diagrams and evidence. Link repository-level
mechanisms into those flows instead of duplicating divergent explanations.
Until those documents exist, track the work and gaps in `RESEARCH.md`.

A first chapter may combine its flow, diagram, connection table, evidence and
failure cases in one document. Scope artifacts to the flow actually examined and
mark broader coverage pending. Split documents when navigation or reuse benefits,
not to satisfy a file-count target. Whole-stack completeness remains the final
objective, not a prerequisite for publishing useful partial research.

Optional supporting documents and diagrams may be added beneath that directory.
Do not create empty boilerplate files merely to satisfy the layout. Link related
repositories without assuming their integration works; cross-repository claims
must identify and inspect the relevant revisions of each participant.

Keep this project specification separate from generated target analyses. Target
repositories remain authoritative for implementation; each analysis is a
revision-scoped explanation, not a promise about the latest branch forever.

## 8. Incremental work and maintenance

Work in small reviewable passes: discovery, architecture, behavior, validation,
then comprehension review. Preserve a current-state note in each target's index
with completed work, open gaps, and the next step. Never call an initial inventory
a completed expert specification.

On target updates, record the old and new revisions, inspect changes and their
transitive impact, and revalidate affected claims. Mark stale sections explicitly
until they are checked. Git history preserves earlier analyses; updates must not
silently change the revision a claim refers to without rechecking it.

Before publishing, inspect the exact staged files and their diff for sensitive
material, unrelated changes, broken references, and unsupported claims. Commit
and push only the authorized analysis. Report local versus remote state honestly.

## 9. Acceptance and comprehension checks

### Chapter-level completion

A single-flow chapter is ready for interim publication when:

- Its scope, pinned revisions and excluded branches are explicit.
- A concrete example explains inputs, transformations, state changes, outputs,
  trust checks, relevant connections and return paths without requiring source.
- Important failure and retry behavior is explained, or specific gaps are labeled.
- Consequential claims have evidence and conflicting evidence is visible.
- A comprehension check records what the reader could answer and what remains
  unresolved; self-review is labeled as such.
- Publication checks pass and the next investigation is recorded.

Unknowns do not block honest interim publication, but unanswered in-scope questions
prevent claiming complete understanding of that mechanism. Discovery inventories
and partial drafts may be published with their actual status; they are not finished
chapters merely because they are committed.

### Comprehensive acceptance

A baseline analysis is ready for comprehensive review when:

- Every required content area is covered or justified as not applicable.
- Scope, pinned revisions, exclusions, and unverified claims are visible.
- Important behavior is explained end to end with concrete examples.
- Whole-stack flows cross repository/node/cluster boundaries explicitly, with
  evidence for each connection and no unexplained handoff between documents.
- Local completion, remote application and final visibility are distinguished;
  missing or proposed integration is not presented as working connectivity.
- Consequential claims have retrievable evidence.
- Implemented behavior, intended behavior, and proposed changes are separated.
- Failure paths, operational limits, and security boundaries are addressed.
- No private data or secrets are included.

A reviewer should then answer, without reading source:

1. What happens from a representative input to its final observable result?
2. What changes in persistent state, and which invariants protect it?
3. What happens if the operation fails halfway through or is repeated?
4. What happens when two relevant operations overlap?
5. Where are identity and permission checks performed, and what is trusted?
6. How can the system be started, tested, diagnosed, and recovered?
7. Which components and contracts would a representative feature change affect?
8. Which important questions remain unanswered, and why?
9. Can I trace one datum across all relevant processes, nodes and clusters,
   identifying each transformation, stored copy, trust check and return message?
10. If a connection fails after one node commits, what do each participant and
    the original caller know, and what makes retry or recovery safe—or unsafe?
11. Which connections are actually wired up at the recorded revisions, which are
    optional, and which exist only as proposals?

Record answers, supporting specification sections, gaps, and review outcome.
Use applicable scenarios rather than inventing distributed-system requirements
for a simple local program. A failed check is useful feedback, not a reason to
conceal uncertainty or claim success.

## 10. Initial scope and next experiment

A first selective research pass is recorded in [RESEARCH.md](RESEARCH.md), with
all six target revisions pinned. [DEVELOPER-GUIDE.md](DEVELOPER-GUIDE.md) explains
selected source-backed mechanisms and their limits. No target tests or services
have been run; full analysis and independent review remain pending.

The next experiment is a complete API-to-storage vertical slice in
`webdawg/open-agent-graph`, followed by deeper coverage of the other repositories
and their evidenced relationships. This order can change with operator direction.
Target execution still requires the permissions and safeguards in section 3.

Questions to resolve through that first exercise:

- Who is the primary reader: maintainer, contributor, operator, or integrator?
- What level of algorithmic and interface detail enables independent reasoning?
- Which document boundaries make the result easiest to navigate?
- How much evidence bookkeeping is useful without obscuring the explanation?
- What review method most reliably detects missing or misleading understanding?

The baseline should evolve from that feedback rather than being treated as a
finished methodology before it has been exercised.

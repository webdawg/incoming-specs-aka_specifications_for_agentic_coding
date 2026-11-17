# Reverse-built software specifications

Status: initial baseline, proposed for review. This is a specification for the
analysis process and its deliverables, not an implementation or a completed
analysis of any target repository.

License: AGPL-3.0-or-later.

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

## 2. What success means

A developer reading the result can explain:

- What the software does, for whom, and where its responsibilities end.
- How its components collaborate and why their boundaries matter.
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
2. Exact commit SHA for every target, branch, and working-tree status.
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

## 4. Method: reconstruct before summarizing

### Phase A — discovery and coverage plan

Inventory entry points, packages, generated/vendor code, executable scripts,
build definitions, dependency manifests, test suites, schemas, configuration,
deployment assets, and existing documentation. Record what will be examined and
why. Explicitly mark omitted areas and their implications.

Identify actual executable paths, not just promising filenames. Determine which
flags, build features, deployment modes, and versions change those paths.

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

Investigate contradictions. Document the observed implementation separately
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
pretending gaps are complete.

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

## 6. Evidence and uncertainty

Assign stable claim identifiers to consequential claims, especially guarantees,
security properties, persistence behavior, and externally visible contracts.
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

## 7. Deliverables and repository layout

Proposed layout for analyses written to this repository:

```text
specs/<owner>/<repository>/
  README.md             # navigation, target revision, scope and reading order
  SPEC.md               # coherent system specification and detailed explanations
  EVIDENCE.md           # claim ledger and verification results
  COVERAGE.md           # examined areas, exclusions, remaining work
  OPEN-QUESTIONS.md     # contradictions, unknowns and next investigations
  REVIEW.md             # comprehension checks, reviewer and acceptance status
```

Optional supporting documents and diagrams may be added beneath that directory.
Do not create empty boilerplate files merely to satisfy the layout. Link related
repositories without assuming their integration works; cross-repository claims
must identify and inspect the relevant revisions of each participant.

Keep this root specification separate from generated target analyses. Target
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

A baseline analysis is ready for review when:

- Every required content area is covered or justified as not applicable.
- Scope, pinned revisions, exclusions, and unverified claims are visible.
- Important behavior is explained end to end with concrete examples.
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

Record answers, supporting specification sections, gaps, and review outcome.
Use applicable scenarios rather than inventing distributed-system requirements
for a simple local program. A failed check is useful feedback, not a reason to
conceal uncertainty or claim success.

## 10. Initial scope and next experiment

No target analysis has been performed. The first experiment is to select one
authorized repository and pinned revision, perform discovery, and produce a
small but substantive first analysis using this baseline. The Open Agent Graph
repositories are possible candidates discussed by the operator, not automatically
authorized targets for execution or blanket multi-repository analysis.

Questions to resolve through that first exercise:

- Who is the primary reader: maintainer, contributor, operator, or integrator?
- What level of algorithmic and interface detail enables independent reasoning?
- Which document boundaries make the result easiest to navigate?
- How much evidence bookkeeping is useful without obscuring the explanation?
- What review method most reliably detects missing or misleading understanding?

The baseline should evolve from that feedback rather than being treated as a
finished methodology before it has been exercised.

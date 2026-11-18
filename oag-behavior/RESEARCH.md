# OAG behavior research ledger

## Status

Baseline primed. No target code was inspected for this new project, no target
builds or tests were run, and no services or migrations were executed. Existing
observations in `oag-developer_understanding/RESEARCH.md` are useful leads, not
silently inherited behavior claims; they must be rechecked and cited when used.

## Starting inputs

The initial target set is the six repositories listed in `SPEC.md`. Before the
first behavior claim, record for each repository:

- remote URL, branch, exact HEAD, and worktree status;
- repository-local instructions and relevant build/test guidance;
- dependency revisions resolved by lockfiles;
- included and excluded subsystems; and
- authorized execution scope and isolation limitations.

## First investigation

Trace one representative local graph operation in `open-agent-graph` from its
public entry point to its event builder, signature/identity checks, transaction,
projector, SQL schema, response, and tests. Use synthetic data. Then trace the
corresponding remote-ingestion path rather than assuming it shares the local
path.

The first chapter should establish:

1. the operation's input and output contract;
2. state before and after success and rejection;
3. transaction and visibility boundaries;
4. duplicate, retry, ordering, and concurrency behavior;
5. what the caller knows at completion; and
6. which later peer/consumer behavior remains unverified.

## Open questions

- Which operation best exposes the core event and projection contract?
- Where are authorization and signature verification performed, and what is trusted?
- Are local and remote event application deliberately symmetric or materially different?
- What does a peer learn after local commit, and how are gaps and retries resolved?
- Which mesh boundaries are runtime paths versus exported library capability?
- Which computations and audio/similarity paths have independent persistence and serving semantics?
- Which Data Roads requirements have no executable implementation?
- What reviewer can perform the independent comprehension check?

## Evidence discipline

Do not copy a claim from the neighboring project without checking its pinned
revision and scope. Preserve contradictions, especially when comments, tests,
lockfiles, and implementation disagree. Update this ledger as actual research
advances; do not mark a behavior complete merely because a source file was read.

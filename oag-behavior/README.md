# OAG behavior

A behavior-first specification of the Open Agent Graph family: what an
operation accepts, what it validates, how state changes, what it returns, what
other nodes or services observe, and what happens when work is repeated,
concurrent, delayed, rejected, or only partly completed.

This is independent of [`oag-developer_understanding`](../oag-developer_understanding/README.md):
that project specifies the reverse-engineering method and developer-facing
explanations; this project specifies the resulting behavioral contracts.

## Current state

Initial baseline only. No target behavior has been declared complete, no target
services have been run, and no new OAG implementation has been created here.
The first pass should use pinned revisions and begin with one representative
local graph operation, then extend through replication, mesh boundaries, and
consumer-visible reads.

## Documents

- [`AGENTS.md`](AGENTS.md) — local contribution rules.
- [`SPEC.md`](SPEC.md) — behavioral scope, model, evidence contract, and acceptance criteria.
- [`RESEARCH.md`](RESEARCH.md) — pinned inputs, coverage, findings, and next investigations.

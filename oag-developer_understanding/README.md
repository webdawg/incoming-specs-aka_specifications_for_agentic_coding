# OAG developer understanding

A specification for agents that reverse-build detailed software specifications
from existing codebases—so developers can understand the software without
having to reconstruct its design by reading the source themselves.

The analyzing agent starts without prior project knowledge, examines the actual
implementation, and explains it with the depth and precision expected of an
expert software developer. Evidence, unknowns, failure modes, and operational
details matter as much as architecture summaries.

## Start here

- [OPERATOR-INTENT.md](OPERATOR-INTENT.md) — the original request, verbatim.
- [SPEC.md](SPEC.md) — initial methodology, output contract, and review criteria.
- [AGENTS.md](AGENTS.md) — instructions for agents contributing here.
- [DEVELOPER-GUIDE.md](DEVELOPER-GUIDE.md) — first source-backed explanations.
- [RESEARCH.md](RESEARCH.md) — pinned revisions, evidence, coverage, and continuation.

## Current state

The baseline and a first selective source-inspection pass are complete. Full
analysis, target test execution, and independent comprehension review are pending.
The selected scope is:

- [open-agent-graph](https://github.com/webdawg/open-agent-graph)
- [oag-mesh](https://github.com/webdawg/oag-mesh)
- [oag-listend](https://github.com/webdawg/oag-listend)
- [oag-collisions](https://github.com/webdawg/oag-collisions)
- [oag-imagination-random-data-compute](https://github.com/webdawg/oag-imagination-random-data-compute)
- [data-roads](https://github.com/webdawg/data-roads)

All six target revisions are pinned in `RESEARCH.md`. The next step is a complete
API-to-storage vertical slice in `open-agent-graph`, then deeper analysis across
the selected set. Target analyses
will live under `specs/<owner>/<repository>/` within this folder. All OAG-specific
specification documents, evidence, diagrams, and review records belong here, not
at the containing repository root.

See the [repository index](../README.md) for other independent specifications.

## License

This project is licensed under the GNU Affero General Public License,
version 3 or later. See the shared [LICENSE](../LICENSE).

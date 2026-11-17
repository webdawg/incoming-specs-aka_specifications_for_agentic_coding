# Incoming Specs — Specifications for Agentic Coding

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

## Current state

The initial baseline is drafted. No target codebase has been analyzed yet.
The next step is to select a target repository and pinned revision, then exercise
and refine the methodology with a real analysis. Target specifications will live
under `specs/<owner>/<repository>/` and remain separate from this root methodology.

## License

This project is licensed under the GNU Affero General Public License,
version 3 or later. See [LICENSE](LICENSE).

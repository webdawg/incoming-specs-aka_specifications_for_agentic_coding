# Agent entry point

Read `OPERATOR-INTENT.md`, `SPEC.md`, and `README.md` before contributing.

- Preserve the quoted operator request verbatim. Keep interpretations and later
  refinements separate from that historical record.
- This public repository develops a reverse-specification methodology and hosts
  revision-scoped analyses. Do not confuse its methodology with a specification
  of a target system that has not yet been examined.
- Confirm target, revision, scope, and execution permissions before analysis.
  Follow target-local instructions and preserve concurrent work.
- Explain mechanisms in enough detail that developers can reason without opening
  source. References support explanations; they do not replace them.
- Follow the evidence and uncertainty requirements in `SPEC.md`. Never fabricate
  author intent, test results, implementation status, or completeness.
- Do not modify target code or run consequential operations as part of discovery
  without separate authorization. Protect secrets and private material even if
  encountered in an otherwise public source.
- Keep target analyses under `specs/<owner>/<repository>/`. Maintain their scope,
  coverage, review status, and next steps as described in `SPEC.md`.
- Inspect Git status and exact staged diffs before committing. Publish only
  authorized content and verify the remote commit after pushing.

Project license: GNU AGPL version 3 or later (`AGPL-3.0-or-later`).

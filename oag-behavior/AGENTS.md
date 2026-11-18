# Agent entry point

Read the parent `AGENTS.md`, then this folder's `README.md`, `SPEC.md`, and
`RESEARCH.md` before contributing.

This is an independent specification project. It is about the observable and
stateful behavior of the Open Agent Graph family, not about the general method
for reverse-building developer understanding. Keep all behavior artifacts inside
this folder.

Behavior must be derived from a pinned implementation revision, tests, executed
observations when authorized, and clearly labeled documented intent. Do not turn
names, comments, diagrams, or adjacent repositories into claims that a behavior
exists. Distinguish implemented behavior, documented intent, proposed behavior,
inference, and unknowns.

Do not modify target repositories or run deployments, migrations, services, or
untrusted setup scripts as part of analysis without separate authorization. Use
synthetic data and isolated execution where appropriate. Never publish secrets,
private configuration, raw session material, or target checkouts into this public
repository.

Preserve concurrent work. Before committing, inspect Git status and the exact
diff, check references and sensitive material, and publish only authorized
specification files. Verify the remote commit after pushing.

Project license: GNU AGPL version 3 or later (`AGPL-3.0-or-later`).

# Agent entry point

Read the parent `AGENTS.md`, then this folder's `OPERATOR-INTENT.md`, `README.md`,
`SPEC.md`, and `RESEARCH.md` before contributing.

This is an independent specification project for openly available crash
analysis. Keep its research and artifacts inside this folder. Preserve the
operator request verbatim and distinguish implemented behavior, requirements,
proposals, inferences, and unknowns.

The purpose is rapid public debugging. Public posting is the default path;
privacy/redaction is an optional mode selected by the uploader. When selected,
a crash report passes through an explicit privacy/redaction boundary before
publication. Do not treat a model, group, or upload path as trusted merely
because it is called a redactor; specify what it receives, what it can retain,
how its result is verified, and what happens when it fails or is uncertain.

Do not upload real crash data, run deployments, or modify target repositories as
part of this baseline. Use synthetic crash reports and synthetic secrets. Protect
credentials and personal data, preserve concurrent work, and inspect exact
diffs before publication.

Project license: GNU AGPL version 3 or later (`AGPL-3.0-or-later`).

# OAG open crashes

A specification for making crash reports rapidly available for public debugging.
The default path is direct upload to a public server. An uploader may select a
privacy mode that sends the report through an OAG group whose sole purpose is to
redact secrets and sensitive content before the report becomes public.

## Current state

Initial baseline only. No crash schema, upload service, optional redaction
group, redaction policy, or public endpoint has been implemented or validated. The
operator's request is preserved in [`OPERATOR-INTENT.md`](OPERATOR-INTENT.md).

## Documents

- [`AGENTS.md`](AGENTS.md) — local contribution and privacy rules.
- [`OPERATOR-INTENT.md`](OPERATOR-INTENT.md) — verbatim operator request.
- [`SPEC.md`](SPEC.md) — initial crash-flow, privacy, and failure requirements.
- [`RESEARCH.md`](RESEARCH.md) — continuation state and open questions.

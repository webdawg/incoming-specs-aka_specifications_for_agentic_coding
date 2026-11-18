# OAG open crashes specification

Status: initial baseline for review. This document defines the desired crash
reporting and privacy boundary; it is not an implemented service or a claim that
public uploads are currently safe.

## 1. Purpose

Make crash reports available quickly enough for broad debugging and collective
improvement. By default, a client should be able to submit a crash report
directly to a public server, where developers and the public can inspect it
without waiting for a private support exchange.

Privacy is an explicit uploader-selected mode, not a mandatory step. When the
uploader selects privacy, the report is sent through an OAG group with exactly
one purpose: redact the incoming crash report before public publication. That
group is a privacy service, not a general discussion group, feature-development
group, or authority to alter technical evidence for convenience. The client or
uploader must be shown clearly whether the selected path is public-by-default or
privacy-redacted.

## 2. Required flow

```text
crash occurs -> capture bounded report -> select publication mode
  -> [default] upload report directly to public server -> public debugging
  -> [privacy selected] upload to redaction group -> inspect and redact ->
     produce redacted report + audit metadata -> publish publicly -> debugging
```

The phrase “directly to a public server” means the default crash-reporting path
should not require a human to manually forward every report. Selecting privacy
changes that path; it is not silently imposed. If privacy is not selected, the
uploader must be warned that the submitted report may contain sensitive content
and that public copies may persist.

For each report, the system must make clear:

1. what the client captured and why;
2. where the raw report was first stored and who can access it;
3. which redaction group or policy processed it;
4. what changed, what was removed, and what evidence might have been lost;
5. how the system knows the report passed the publication gate; and
6. which public identifier links the sanitized report to discussion without
   exposing private identifiers or raw content.

## 3. Optional redaction group boundary

The redaction group's one thing in the universe is redaction. It is used only
when the uploader selects privacy. Its permitted function is to inspect an
incoming report, identify sensitive material, remove or transform it according
to a versioned policy, and emit a sanitized report.
It must not:

- publish the raw report;
- use crash contents for unrelated profiling or product decisions;
- silently rewrite technical facts to make the project look better;
- infer permission to disclose information merely because a crash happened;
- grant itself broad access to unrelated OAG data; or
- treat a failed, uncertain, or unavailable redaction as safe to publish.

The exact implementation may be an OAG group of agents, services, or authorized
people, but the boundary and least-purpose rule must remain explicit. A group
name alone is not proof that its members or code obey this purpose.

## 4. Sensitive content to consider

The redaction policy must investigate, at minimum:

- passwords, tokens, private keys, cookies, session material, and credentials;
- personal names, email addresses, precise locations, contact data, and user text;
- file contents, home-directory paths, environment variables, command arguments,
  URLs with embedded secrets, and authorization headers;
- database records, private network addresses, host identifiers, and local
  usernames where disclosure is unnecessary;
- screenshots, memory fragments, attachments, logs, core dumps, and stack data
  that may contain arbitrary bytes; and
- timestamps or combinations of fields that create identifying or security risk.

Redaction must not rely only on matching a short list of secret names. It should
have bounded size and type handling, deterministic replacement where useful,
explicit uncertainty outcomes, and a way to preserve enough structure for
reproduction and diagnosis. “No obvious secret was found” is not equivalent to
“safe for public release.”

## 5. Public report contract

A published report should have a stable public identifier, crash/build/runtime
context, sanitized stack or trace data, structured failure information, redaction
policy version, submission and publication times, and an indication of what was
unavailable or removed. It must not contain raw decryption keys or imply that
redaction proves all sensitive information was removed.

The public server should support bounded payloads, authentication of the upload
where required, deduplication, rate limits, abuse handling, retention and
removal policy, and clear separation between raw, redaction-stage, and public
copies. Public availability does not grant permission to republish personal
content discovered in a crash.

## 6. Failure and privacy semantics

- If privacy was selected and the redaction group is unavailable, the report
  remains private or is discarded according to an explicit retention policy; it
  is not silently published raw.
- If privacy was selected and redaction is uncertain, the result is blocked or
  held for review.
- If privacy was not selected, direct public upload may proceed according to the
  default path; the system must record and display that no redaction was requested.
- If a report is accidentally published with sensitive content, the system must
  support public withdrawal, access-log review, notification, and incident
  handling, while acknowledging that copies may persist.
- If a client retries, the report retains a stable identity and must not create
  unlimited duplicate public records.
- If upload fails, the client must state whether the report remains locally,
  privately queued, or lost; it must not silently claim public availability.
- A corrupted or truncated report must be labeled as such rather than repaired
  into an apparently authoritative record.
- Redaction must be observable enough to audit without exposing the very data it
  removed.

## 7. Evidence and uncertainty

Use stable claim IDs such as `C-001`. For every security, privacy, durability,
or public-behavior claim, record the target revision, source location, evidence
type, verification command or test, and limitations. Distinguish:

- **Observed** — directly exercised under recorded conditions.
- **Implemented** — supported by inspected code but not necessarily exercised.
- **Documented intent** — stated by an operator or existing documentation.
- **Inferred** — reasoned interpretation with uncertainty stated.
- **Proposed** — future behavior, not current behavior.
- **Unknown** — insufficient evidence and a specified next investigation.

## 8. Non-goals for this baseline

This baseline does not select a redaction algorithm, OAG group protocol, public
server, retention duration, identity model, or legal/compliance regime. It does
not authorize uploading real crash data or collecting core dumps by default.
It does define direct public posting as the default path, with privacy/redaction
available only when explicitly selected; selecting privacy is not a guarantee
that all sensitive information can be removed, and public exposure remains
irreversible in practice once copies exist.

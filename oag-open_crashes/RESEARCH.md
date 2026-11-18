# OAG open crashes research ledger

## Status

Baseline primed. No real crash reports, core dumps, services, tests, or public
uploads have been used for this project.

## First research questions

- What is the minimum useful crash payload, and which capture sources can contain
  arbitrary sensitive bytes?
- How is the public-by-default choice presented, confirmed, and recorded?
- Is the optional redaction group implemented as a cryptographic OAG group, a
  service boundary, human review, or a combination?
- How is group membership authorized, audited, rotated, and prevented from
  becoming a general data-access path?
- Which redaction rules are deterministic, and how are uncertain cases blocked?
- How can developers verify that public output derives from a particular private
  report without exposing the private report or its secrets?
- What raw-data retention, deletion, incident response, and public withdrawal
  guarantees are realistic?
- How should redaction handle native core dumps, screenshots, memory snippets,
  encrypted fields, and user-supplied payloads?
- What OAG event, identity, storage, and public-server mechanisms actually exist,
  rather than merely being suggested by repository names?

## Next safe step

Create synthetic crash fixtures containing fake credentials, personal data,
paths, memory-like bytes, and ordinary debugging context. Define the explicit
public-by-default confirmation and expected optional-redaction outputs and
blocked cases before examining or implementing a live upload path. Do not use
real secrets or real user crash data.

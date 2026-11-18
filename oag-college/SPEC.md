# OAG college specification

Status: initial baseline for review. This is a statement of purpose and
requirements, not an implemented educational platform.

## 1. Purpose

Make useful knowledge freely available to everyone, forever. “College” is a
metaphor for an open, shared place of learning—not a requirement to reproduce a
traditional institution, credential gate, tuition model, or admissions barrier.

The system should help people discover, understand, question, practice, teach,
and preserve knowledge. It should serve different levels of prior knowledge,
languages, abilities, devices, and connectivity.

“Forever” is a preservation goal, not a promise that any single server, format,
organization, or political environment cannot fail. The design must make
continuity practical through copies, open formats, export, replication,
provenance, migration, and public stewardship.

## 2. Knowledge-commons principles

- **Free access:** core knowledge should be available without payment or
  arbitrary membership barriers.
- **Copy and preservation:** people and institutions should be able to copy,
  mirror, export, and preserve the material under clear terms.
- **Open formats:** essential content must not depend on one proprietary reader,
  service, or hidden database.
- **Provenance:** distinguish source material, interpretation, teaching aids,
  uncertainty, correction, and opinion.
- **Plural access:** support text, audio, visual, translated, low-bandwidth,
  offline, and assistive forms where practical.
- **Human agency:** learners should be able to inspect sources, disagree,
  contribute, leave, and continue learning elsewhere.
- **No single point of control:** no one operator should be able to erase the
  entire knowledge commons or silently rewrite its history.
- **Safety and rights:** openness does not remove privacy, copyright, consent,
  security, or responsible handling of dangerous information.

## 3. Minimum behavior to specify

For each knowledge area, document:

1. **Content identity** — title, author or contributor, version, source, license,
   language, subject, prerequisites, and stable identifiers.
2. **Learning path** — how a person discovers material, understands its level,
   follows prerequisites, practices, asks questions, and receives feedback.
3. **Evidence and disagreement** — citations, competing views, uncertainty,
   corrections, review history, and separation of fact from interpretation.
4. **Contribution** — how people submit changes, translations, examples,
   annotations, corrections, and challenges without surrendering all control.
5. **Preservation** — export, mirroring, snapshots, integrity checks, format
   migration, recovery, and what happens when a host or maintainer disappears.
6. **Access** — authentication requirements, offline use, accessibility,
   bandwidth, indexing, rate limits, and protection against exclusion.
7. **Governance** — who can moderate, alter metadata, resolve disputes, remove
   material, or declare a version authoritative; all such powers must be visible.
8. **Licensing** — whether a person can legally copy, teach, translate, remix,
   archive, and redistribute the material.

## 4. Proposed flow

```text
question -> discover knowledge -> inspect provenance and context ->
learn/practice/discuss -> contribute or correct -> review and version ->
replicate/export/preserve -> future learners access it
```

This is a planning model, not a claim that any OAG component implements it.
Automated explanations must identify their sources and uncertainty. An agent may
help teach, but it must not fabricate authority, conceal disagreement, or make
access to essential knowledge depend on trusting the agent.

## 5. Permanence and failure semantics

- A public copy is not the same as preservation; replicas must be discoverable,
  verifiable, and independently restorable.
- A source disappearing must not erase every preserved copy.
- A format becoming obsolete must trigger migration or retain a usable open
  representation.
- A disagreement or correction must preserve history and explain what changed,
  rather than silently rewriting the record.
- A broken mirror, corrupted object, unavailable translator, or failed index must
  degrade access without destroying the canonical or preserved material.
- Sensitive or restricted material must not be made public merely to satisfy the
  word “forever”; privacy, safety, and lawful rights require explicit handling.

## 6. Evidence and uncertainty

Use stable claim IDs such as `K-001`. For each consequential claim, record the
source, revision, evidence type, verification, license or access conditions, and
limitations. Distinguish:

- **Observed** — directly exercised under recorded conditions.
- **Implemented** — supported by inspected code but not necessarily exercised.
- **Documented intent** — stated by an operator or existing documentation.
- **Inferred** — reasoned interpretation with uncertainty stated.
- **Proposed** — future behavior, not current behavior.
- **Unknown** — insufficient evidence and a specified next investigation.

## 7. Non-goals for this baseline

This baseline does not select a curriculum, educational credential, database,
replication protocol, archive institution, licensing scheme, moderation policy,
or OAG integration. It does not authorize copying private or copyrighted material
without permission, publishing personal data, or claiming literal physical
permanence for any particular service.

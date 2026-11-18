# OAG vote specification

Status: initial baseline for review. This is a specification of the problem and
boundaries, not an implemented voting system.

## 1. Purpose

Enable humans to decide things together. A future system may help people frame a
question, inspect relevant evidence, discuss alternatives, express preferences,
cast or delegate a vote, count according to an explicit rule, and inspect the
result. The system must preserve meaningful human agency throughout.

The central requirement is not merely that a button labeled “vote” exists. A
person must be able to understand what is being decided, what choices exist,
what information is being used, how participation affects the result, and what
happened after the decision.

## 2. Human authority and agent boundaries

- Agents may summarize, explain, organize, calculate, and identify uncertainty,
  but must identify those contributions as assistance.
- An agent must not silently cast a human's vote, infer consent from inactivity,
  or turn a recommendation into a decision.
- The decision rule, eligibility, timing, quorum, delegation, and outcome meaning
  must be explicit before a decision is treated as authoritative.
- People must be able to inspect relevant alternatives, revise an eligible vote
  before the stated close, abstain where supported, and understand applicable
  limitations.
- A human decision is not made legitimate merely because a model predicted it or
  because a majority was produced by an opaque process.
- The system must not use the human-behavior modeling boundary from related work
  to pressure, deceive, target vulnerabilities, suppress participation, or
  manipulate a population into a desired result.

These are initial safeguards, not a selection of a particular voting method.

## 3. Minimum behavior to specify

For each decision process, document:

1. **Question and alternatives** — what is being decided and what each option
   means, including whether options are exhaustive or an abstention is possible.
2. **Authority and eligibility** — who may propose, deliberate, vote, delegate,
   challenge, close, and publish; how eligibility is established and changed.
3. **Information** — evidence sources, provenance, timestamps, uncertainty,
   summaries, corrections, conflicts of interest, and what voters cannot know.
4. **Ballot semantics** — anonymous or attributed voting, revocation, delegation,
   weight, quorum, tie handling, and protection against duplicate ballots.
5. **Counting and closure** — the exact rule, authoritative clock, close
   conditions, recount behavior, invalid ballots, and finalization boundary.
6. **Results and audit** — what participants and observers can verify, what is
   private, how the result relates to the underlying decision, and how disputes
   are handled.
7. **Failure and recovery** — network loss, partial submission, replay, stale
   clients, compromised accounts, unavailable evidence, changed eligibility,
   and restoration from backup.
8. **Human impact** — accessibility, language, power asymmetries, coercion risk,
   minority protections, retaliation risk, and whether participation is truly
   voluntary.

## 4. Proposed flow

```text
human question -> proposal and alternatives -> evidence/discussion ->
eligibility and participation -> human ballot -> explicit count ->
verifiable result -> human-readable decision record
```

This flow is a planning model, not a claim that any OAG component currently
implements it. An agent-generated summary or recommendation must remain visibly
different from a human-authored proposal and a human decision.

## 5. Evidence and uncertainty

Behavioral claims use stable IDs such as `V-001` and record the target revision,
source location, evidence type, verification command or test, and limitations.
Use these labels:

- **Observed** — directly exercised under recorded conditions.
- **Implemented** — supported by inspected code but not necessarily exercised.
- **Documented intent** — stated by an operator or existing documentation.
- **Inferred** — reasoned interpretation with its uncertainty stated.
- **Proposed** — future behavior, not current behavior.
- **Unknown** — insufficient evidence and an explicit next question.

## 6. Non-goals for this baseline

This baseline does not select:

- a voting method or governance constitution;
- identity, anonymity, or cryptographic ballot technology;
- a quorum, delegation, weighting, or eligibility policy;
- an OAG integration or deployment topology; or
- an agent authority to decide in place of humans.

It also does not authorize implementation, deployment, collection of real voting
records, or manipulation of participation or outcomes.

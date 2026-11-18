# Agent entry point

Read the parent `AGENTS.md`, then this folder's `OPERATOR-INTENT.md`, `README.md`
and `SPEC.md`, followed by `DECISIONS.md` and `ENCRYPTION-ARCHITECTURE.md`.
Preserve the original operator statement verbatim.

This project is a location-linking service specification, not the separate OAG
developer-understanding project. Keep research, designs and continuation state
inside this folder. Update the README's current state when work progresses.

Distinguish operator requirements, proposed interpretations, research findings
and implemented behavior. Do not select cryptography, location precision, expiry
semantics or OAG integration silently. Investigate established protocols and
maintained libraries before designing security mechanisms. Keep encryption choices
revisable: separate product security goals from mechanisms, document protocol
versions and migration limits, and never silently downgrade protection. Do not
treat MLS as selected or promise transparent substitution between incompatible
protocols. Public plain-text delivery carries ciphertext, not group plaintext.

Protect location and group-key privacy. Use synthetic examples, never real user
locations, credentials or group secrets. Public ciphertext is not public plaintext.
Service expiry cannot guarantee deletion of copies or keys retained by recipients.

This baseline authorizes specification work, not deployment or implementation.
Record research sources, versions, limitations and unanswered questions honestly.

License: AGPL-3.0-or-later.

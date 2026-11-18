# oag-location-to-legacy

A public location-linking service: send a location and receive associated data,
including chats and alerts. Planned interfaces include a live public plain-text
feed of encoded group ciphertext and a small Android application. Shared access
enables local decryption. Forward secrecy and recovery after compromise are security
goals; the encryption protocol and architecture remain revisable. Records have a
limited serving lifetime whose exact semantics remain to be decided.

## Documents

- [OPERATOR-INTENT.md](OPERATOR-INTENT.md) — original description, verbatim.
- [SPEC.md](SPEC.md) — initial requirements, proposed flows, research plan and questions.
- [ENCRYPTION-ARCHITECTURE.md](ENCRYPTION-ARCHITECTURE.md) — changeable encryption,
  key lifecycle, protocol boundaries and migration requirements.
- [DECISIONS.md](DECISIONS.md) — subsequent clarifications and open decisions.
- [AGENTS.md](AGENTS.md) — contributor instructions.

## Current state and next step

Expanded baseline drafted. No external research, protocol selection, implementation
or testing has occurred for this project. Public ciphertext delivery is established;
admission policy, exact key-sharing mechanics, expiry parameters, geographic
matching and OAG integration remain open. Start with `DECISIONS.md` and section 10
of `SPEC.md`, then research the agreed scope. MLS is a candidate, not a selection.

This is independent of the developer-understanding specification. Keep all its
artifacts in this folder. See the [repository index](../README.md).

License: [AGPL-3.0-or-later](../LICENSE).

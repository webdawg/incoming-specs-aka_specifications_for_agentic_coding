# oag-location-to-legacy

A public location-linking service: send a location and receive associated data,
including chats and alerts. Planned interfaces include a live public plain-text
feed and a small Android application. Group key sharing enables encrypted content;
records have a limited serving lifetime whose exact semantics remain to be decided.

## Documents

- [OPERATOR-INTENT.md](OPERATOR-INTENT.md) — original description, verbatim.
- [SPEC.md](SPEC.md) — initial requirements, proposed flows, research plan and questions.
- [AGENTS.md](AGENTS.md) — contributor instructions.

## Current state and next step

Initial baseline drafted. No external research, protocol selection, implementation
or testing has occurred for this project. Public/group visibility, key-sharing
semantics, expiry guarantees, geographic matching and OAG integration remain open.
Start with the decisions in section 10 of `SPEC.md`, then research the agreed scope.

This is independent of the developer-understanding specification. Keep all its
artifacts in this folder. See the [repository index](../README.md).

License: [AGPL-3.0-or-later](../LICENSE).

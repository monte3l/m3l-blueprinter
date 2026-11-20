# Decisions

Decisions that shaped this project, one file each, in the order they were made.
A record says what was decided, what was considered and why, so the question
does not return without new facts. The format is the minimal MADR
([adr.github.io/madr](https://adr.github.io/madr/)): copy `0000-template.md`,
number it and fill it in.

A record is not edited once it is accepted. A later decision that replaces it
says so, and the old one is marked superseded with a link forward.

| Record                                   | Status   | Decision                                                                   |
| ---------------------------------------- | -------- | -------------------------------------------------------------------------- |
| [0001](0001-claude-code-plugin-form.md)  | accepted | Ship m3l-blueprinter as a Claude Code plugin with a CLI and an npm package |
| [0002](0002-one-model-two-emitters.md)   | accepted | Keep one architecture model and emit Mermaid and D2 SVG from it            |
| [0003](0003-vendored-policy-pack.md)     | accepted | Vendor the policy pack into each project and pin it by hash                |
| [0004](0004-committed-blueprint-page.md) | accepted | Commit the blueprint page rather than generating it in the docs build      |

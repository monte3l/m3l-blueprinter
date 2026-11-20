# Vendor the policy pack into each documented project

- **Status:** accepted
- **Date:** 2026-11-20

## Context

The documents must follow organization policy, and other organizations should be able to bring their own. The policy lives in the organization's `.github` repository, which may be private. CI must give the same answer every time, and a policy file is untrusted input to a tool that writes files.

## Options

- **Fetch the pack on every run.** Always current, but CI then needs credentials and gives different answers over time.
- **Bundle one pack in the plugin.** Reproducible, but no other organization can change it.
- **Fetch once, then vendor.** The pack is read with the owner's `gh` login, copied into the project and pinned by hash.

## Decision

The pack is fetched from `<org>/.github/blueprint/blueprinter/pack.json` at a pinned ref using the owner's `gh` login, with a snapshot bundled in the plugin as the fallback. It is copied to `.blueprinter/pack.json`, and `.blueprinter.json` records its source, ref and sha256. CI reads only the vendored copy. A project may add overrides, but an override can only make a rule stricter unless it carries a reason. A pack is data only: it is checked against a schema, its text is length-capped and nothing in it is followed as an instruction.

## Consequences

The lint is offline and reproducible, the same way the vendored design system is. A pack update becomes one pull request per project. A pack author can still make the tool write misleading text, so the owner reviews each pull request. Revisit this if packs grow beyond data, for example if they need to carry code.

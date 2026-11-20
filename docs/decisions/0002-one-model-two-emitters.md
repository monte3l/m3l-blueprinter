# Keep one architecture model and emit two diagram formats

- **Status:** accepted
- **Date:** 2026-11-20

## Context

Diagrams must show on GitHub and on the docs site, and they must not drift from each other or from the code. GitHub renders only Mermaid in Markdown. Mermaid's C4 syntax is unfinished and `architecture-beta` is beta, and Mermaid theming accepts hex colours only. D2 renders themed SVG through WebAssembly with no browser.

## Options

- **Mermaid only.** Works on GitHub, but the site cannot show it and theming is weak.
- **D2 or SVG only.** Looks right on the site, but GitHub shows nothing in a Markdown fence.
- **One model, two emitters.** A `model.json` in the C4 shape is the source of truth, a pure TypeScript emitter writes Mermaid `flowchart`, and a D2 emitter writes SVG.

## Decision

One model, two emitters. `docs/architecture/model.json` holds the elements, relationships and views. Mermaid uses `flowchart` with colours from the DTCG token files, and D2 writes one SVG per view with light and dark themes. Both carry a stamp with the model hash and emitter version, and the lint recomputes the hash without re-rendering.

## Consequences

CI needs no renderer, because the lint compares hashes only. D2 is an optional dependency and, if it fails, Mermaid still works. Two outputs have to stay visually consistent, which the contrast and stamp rules check. Revisit this when GitHub renders more than Mermaid or Mermaid's C4 syntax is finished.

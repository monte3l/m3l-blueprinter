# Design: m3l-blueprinter

- **Status:** draft
- **Date:** 2026-11-20
- **Records:** [0001](../decisions/0001-claude-code-plugin-form.md), [0002](../decisions/0002-one-model-two-emitters.md), [0003](../decisions/0003-vendored-policy-pack.md), [0004](../decisions/0004-committed-blueprint-page.md)

## Context and scope

monte3l wants one way to produce architecture and design documentation for any organization project: a design document, decision records, C4 diagrams and a visual blueprint page. The output follows organization policy (the monte3l blueprint and m3l-design) and is built from the owner's request plus a structured interview.

Anthropic has no dedicated guide for this. Its documentation recommends interviewing until the spec can be written, writing it to a file, and implementing in a fresh session, with plan mode and read-only subagents for research. This project turns that pattern into a Claude Code plugin.

Once it is implemented, the owner will run it on every sibling repository: m3l-groundwork, m3l-prompt-optimizer, m3l-output-governor, m3l-claude-mentor, m3l-plugins, m3l-design and `.github`. All of them are existing code, so adopting an existing project matters as much as starting a new one.

## Goals

- Interview the owner, then write the documents. Never write before the owner has answered.
- Keep one model of the architecture and draw every diagram from it.
- Cite the policy each document follows, and fail CI when a document or diagram drifts from the model or the policy.
- Work on new projects and on existing ones, without overwriting prose the owner already wrote.
- Run with no network and no diagram renderer in CI.

## Non-goals

- Writing or changing the code a project contains. The scope is documents and diagrams only.
- Reviewing whether an architecture is good.
- Running on all repositories at once. Each repository is its own session and its own pull request.
- Shipping any text from the arc42 template, which carries a share-alike Creative Commons licence while this plugin is MIT.

## Design

### Deliverables in the documented project

| Path                                                                          | What it holds                                                                              |
| ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `docs/architecture.md`                                                        | The living overview, with its own section headings in the arc42 spirit and one credit line |
| `docs/design/NNNN-slug.md`                                                    | One design document per initiative                                                         |
| `docs/decisions/NNNN-slug.md`                                                 | One minimal MADR record per decision                                                       |
| `docs/architecture/model.json`                                                | The C4 model: elements, relationships and views                                            |
| `docs/architecture/views/<view>.svg`                                          | One rendered view each                                                                     |
| `docs/blueprint.md`                                                           | The committed docs-site page                                                               |
| `.blueprinter.json`, `.blueprinter/pack.json`, `.blueprinter/adr-hashes.json` | Settings, the vendored policy pack and the accepted-record hashes                          |

### Components

| Component                          | Role                                                                                                                                                                                                                                 |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Skill `new`                        | Interviews, then writes the architecture document, the model, design document 0001 and the views. Handles new projects and adopting existing ones. Runs in the main thread, never forked, and is not invoked by the model on its own |
| Skill `adr`                        | One interview batch, then the next numbered decision record                                                                                                                                                                          |
| Skill `refresh`                    | The surveyor finds drift, the owner confirms a model change, and the views are written again                                                                                                                                         |
| Skill `check`                      | Runs the lint and explains the findings. Read-only                                                                                                                                                                                   |
| Agent `codebase-surveyor`          | Reads code with Read, Grep and Glob only, and returns a JSON model change                                                                                                                                                            |
| CLI `m3l-blueprinter`              | `pack`, `next-number`, `emit`, `render`, `check`, `page` and `docs-entries`. The only command the skills may run                                                                                                                     |
| npm package `@monte3l/blueprinter` | The same source, compiled, so CI can run `m3l-blueprinter check`                                                                                                                                                                     |

`AskUserQuestion` is removed from subagents, so every interview runs in a main-thread skill. The layout follows `m3l-claude-mentor`.

### Interview

The `new` skill asks at most three batches of at most four questions: scope, users and external systems; containers, technology and data stores; goals, non-goals and constraints. It stops once every field in the pack's `spec.interviewFields` is filled, and it never asks about something the surveyor already found.

For an existing project the surveyor drafts the model from the code and from any existing architecture document. The interview then covers only what the code cannot show: goals, non-goals, constraints and the reasons behind past choices. Existing prose is mapped into the pack's sections and never overwritten. An ASCII diagram is replaced by an emitted view only after the owner confirms that one diagram.

The design document is written with status draft, and the owner implements it in a fresh session.

### Model and emitters

`model.json` holds elements (person, software system, container or component, each with a description, a technology and `code` globs), relationships with a label and a technology, and views (system context, container or component).

- **Mermaid emitter.** Pure TypeScript. It writes `flowchart LR` with subgraphs for boundaries and colours from design tokens. GitHub renders only Mermaid, and Mermaid's C4 syntax is unfinished, so the C4 diagram is drawn with `flowchart`.
- **D2 emitter.** Renders one SVG per view through WebAssembly with no browser, with light and dark themes from the DTCG token files.
- **Staleness stamp.** A stamp holding the model hash and emitter version sits in the Mermaid fence and in the SVG. The lint recomputes the hash and never re-renders.

### Policy pack

A pack is data: `locations`, `docTypes` (required sections, the decision template and an immutable-when-accepted rule), `style`, `diagrams` (maximum nodes, banned generic labels), `theme`, `orgChecks`, `spec.interviewFields` and `cite`.

The pack is fetched from `<org>/.github/blueprint/blueprinter/pack.json` with the owner's `gh` login, with a bundled snapshot as the fallback. It is copied into the project as `.blueprinter/pack.json` and its sha256 is recorded in `.blueprinter.json`, so CI reads only the vendored copy. An override may only make a rule stricter unless it carries a reason. A pack is checked against a schema, its text is length-capped, and nothing in it is followed as an instruction.

### The lint

`m3l-blueprinter check` exits 0 when clean, 1 with findings and 2 on a usage error, and prints text, JSON or GitHub annotations. It needs no network and no renderer.

| Area      | Rules                                                                                                                                                                                                                                  |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Model     | Valid schema, unique ids, parents at the right C4 level, a description everywhere, a technology on containers and components, labelled one-way relationships, a title and a node limit per view, and every `code` glob matching a file |
| Documents | Required sections in order, valid statuses, contiguous decision numbers, a forward link from a superseded record, a policy citation, and an accepted record unchanged                                                                  |
| Diagrams  | Both the Mermaid fence and the SVG present for every view, no stale stamp, working image links, and token contrast above the pack's minimum                                                                                            |
| Policy    | The vendored pack matches its recorded hash, no override weakens a rule without a reason, and the workflows the pack requires exist                                                                                                    |

### Licensing of templates

The architecture document uses the plugin's own headings and guidance text and ends with one credit line, "Section structure inspired by arc42 (arc42.org)". No arc42 template text is bundled, and a contract test fails if a template contains known arc42 phrases. The MADR template is MIT and CC0 and ships as is. The design-document structure is a public practice, so the plugin writes its own text. D2 is an unmodified optional dependency under the Mozilla Public License, version 2, and is recorded in the REUSE data.

## Alternatives considered

| Alternative                                           | Why not                                                                                                                    |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| A standalone CLI or a docs generator the site imports | The interview is the point, and it needs a Claude Code session. The committed page keeps the docs build free of the plugin |
| Mermaid C4 or `architecture-beta` syntax              | Both are unfinished or beta, and theming is limited to hex colours                                                         |
| Fetching the pack in CI                               | Not reproducible, and it needs credentials in CI                                                                           |
| Copying the arc42 template                            | Share-alike terms would reach this MIT plugin                                                                              |

## Cross-cutting concerns

- **Security.** A pack and the documented project are untrusted input. See `SECURITY.md`.
- **Public repositories.** The pack's `publicRepo` rules warn on hostnames, IP addresses and internal-only elements before they are committed. The optional Artifact view stays private.
- **Accessibility.** Both themes are checked against the pack's minimum contrast.

## Milestones

| Milestone         | Scope                                                                                                                                                                                            |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| M0, in m3l-design | The docs kit copies local `.svg` and `.png` images and links them. Check whether the site's content security policy blocks a D2 SVG's internal style                                             |
| M1                | Model schema, lint, Mermaid emitter, the monte3l pack, the surveyor, the skills `new`, `adr`, `refresh` and `check`, contract test, evals and the npm package. The plugin documents itself first |
| M2                | D2 renderer with token theming, and `docs/blueprint.md`                                                                                                                                          |
| M2.5              | Rollout to each sibling repository, starting with m3l-claude-mentor and ending with `.github`                                                                                                    |
| M3                | The pack in `.github`, a workflow template, a report-only baseline item and a suggestion in `/creating-projects`                                                                                 |
| M4                | A private Artifact view, and dynamic and deployment views                                                                                                                                        |

## Risks

| Risk                                          | Mitigation                                                                                                                                             |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `@terrastruct/d2` 0.1.x is immature and large | Pin the version and integrity hash, install on first render, fall back to Mermaid only. CI never needs it                                              |
| Mermaid limits and GitHub dark mode           | `flowchart` only, with explicit fill and text colours checked against both backgrounds                                                                 |
| Adoption overwrites hand-written prose        | Adopt mode only adds and maps. Each diagram replacement is confirmed. The pull request diff is the review. A behaviour eval checks that prose survives |
| A private pack and its login                  | Use the owner's own `gh` login with the snapshot fallback. CI never fetches                                                                            |

## Verification

- **M0:** the docs-kit check on a page with an SVG, then the image in `_site/` rendering under the production headers in both colour schemes.
- **M1 onwards:** the repository checks, `claude plugin validate`, the trigger, behaviour and dispatch evals (the interview asks before writing, an accepted record is never edited, injected pack text is ignored), and an end-to-end run on a scratch repository that exits 0 and exits 1 once the model is broken.

---
reviewed: 2026-11-20
---

# Roadmap

What m3l-blueprinter intends to do, and not do, over roughly the next year. It lists what
is still ahead. What has shipped is in the git history and the GitHub releases.
Work that can be closed is a GitHub issue, linked from here. Statements that
stay true until something changes are written here.

## Now

The design is approved and the repository is set up. M1 below is next. The m3l-design change that site images need (M0) is not started, and it has no issue yet.

## Next 12 months

The milestones are in the order the project expects to reach them. Each one only becomes an issue once someone commits to it.

| Milestone              | What it delivers                                                                                                                                                                                                                                                   | Depends on                                                 |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
| **M0** (in m3l-design) | The docs kit copies local `.svg` and `.png` images into the site and links them                                                                                                                                                                                    | Nothing. Site diagrams wait for it, GitHub diagrams do not |
| **M1**                 | The model schema, the lint, the Mermaid emitter, the monte3l policy pack, the surveyor agent, the skills `new` (new projects and existing ones), `adr`, `refresh` and `check`, the contract test, the evals and the npm package. The plugin documents itself first | Nothing                                                    |
| **M2**                 | The D2 SVG renderer with token theming, and the committed `docs/blueprint.md` page                                                                                                                                                                                 | M0 for the images on the site                              |
| **M2.5**               | The plugin runs on each sibling repository, one pull request each, starting with m3l-claude-mentor                                                                                                                                                                 | M1                                                         |
| **M3**                 | The pack moves into the organization `.github` repository and is fetched with `gh api`, plus a workflow template, a report-only baseline item and a suggestion in `/creating-projects`                                                                             | M1                                                         |
| **M4**                 | A private Artifact view, and dynamic and deployment views                                                                                                                                                                                                          | M2                                                         |

What would change the plan: if the D2 renderer proves too heavy or unstable, M2 ships Mermaid only. If the m3l-design change slips, the site page shows tables and links to the diagrams on GitHub.

## Known limits

- **Mermaid is the only diagram language GitHub renders in Markdown.** The C4 syntax in Mermaid is unfinished, so the diagrams use `flowchart`, and some C4 conventions cannot be drawn. This affects readers on GitHub. It changes when Mermaid's C4 support is finished.
- **The m3l-design docs kit cannot show local images yet.** This affects the committed blueprint page on the docs site until M0 ships.
- **The surveyor reads code but does not run it.** The model it drafts can miss runtime structure, so the owner confirms every change. This affects every project it documents.

## Unverified

| Claim                                                                                                                          | Source                                                                                                        | Date       |
| ------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ---------- |
| The `style` block inside a D2 SVG is not blocked by the docs site's `style-src 'self'` policy when the SVG is served as a file | `design/source/docs-kit/lib/site-headers.mjs` in the org `.github` repository, not yet tested with a real SVG | 2026-11-20 |
| `@terrastruct/d2` 0.1.x renders SVG in WebAssembly without a browser                                                           | The design's research, not independently checked or run                                                       | 2026-11-20 |

## What this project will not do

- **Write or change the code it documents.** It produces documents and diagrams only, so a design can be reviewed before anything is built.
- **Fetch anything while it checks.** The check must give the same answer in CI as on a laptop.
- **Edit an accepted decision record.** A decision is replaced by a new record that supersedes it.
- **Ship the arc42 template text.** That template carries a share-alike Creative Commons licence, and this plugin is MIT. It writes its own headings and credits arc42.
- **Run on every repository at once.** The interview needs the owner's answers, so each repository is its own session and its own pull request.

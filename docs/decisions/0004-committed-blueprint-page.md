# Commit the blueprint page instead of generating it in the docs build

- **Status:** accepted
- **Date:** 2026-11-20

## Context

Each project needs a visual blueprint page on its docs site. The m3l-design docs kit builds the site from restricted Markdown: it escapes raw HTML, blocks scripts, serves a strict content security policy, and today copies no local image files. Pages come from Markdown files or from generators that return a summary and Markdown. The site also publishes a Markdown copy of every page for agents.

## Options

- **A generator the docs build imports.** Always current, but every docs build would depend on the plugin.
- **A claude.ai Artifact only.** Rich, but private, needing a login, and not part of the site.
- **A committed `docs/blueprint.md` page.** Plain Markdown with the SVG views and element and relationship tables, written by the plugin.

## Decision

The plugin writes `docs/blueprint.md` and commits it. It holds the SVG images plus tables, so the docs build stays independent of the plugin and the Markdown copy for agents comes for free. An optional private Artifact view is a later milestone. A `docs-entries` command prints the `pages` and `exclude` entries, each with a one-line summary, for the skill to add to `docs.config.mjs`. Until the docs kit can copy local images, the page shows the tables and links to the architecture document on GitHub.

## Consequences

The page can go stale, so the lint checks its stamp like any other view. Showing images on the site depends on an m3l-design change, listed as M0. Revisit this if the docs kit gains a way to run generators from other repositories.

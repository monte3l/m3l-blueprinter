# Ship m3l-blueprinter as a Claude Code plugin

- **Status:** accepted
- **Date:** 2026-11-20

## Context

The tool has to interview the owner and then write documents. Anthropic's guidance for this work is to interview until the spec can be written, write it to a file and implement in a fresh session. It also removes `AskUserQuestion` from subagents, so an interview cannot run in one. The deterministic parts, such as numbering and checking, must also run in CI.

## Options

- **A standalone CLI.** Easy to run in CI, but it has no interview and no model to ask the owner.
- **A skill set in each repository.** Interviews work, but every repository carries its own copy and drifts.
- **A Claude Code plugin with a CLI.** Skills run the interview in the main thread, and the same CLI, published to npm, runs the check in CI.

## Decision

A plugin, listed in m3l-plugins, with four skills, one read-only surveyor agent and a CLI. The skills are the interview and the writing. The CLI is the only command they may run, and it holds everything deterministic. The npm package `@monte3l/blueprinter` ships the same source compiled, because Node does not strip types inside `node_modules`.

## Consequences

The interview skills must stay in the main thread and never be forked. One plugin release updates every repository that installs it. CI needs the npm package pinned in a lockfile so Dependabot can bump it. The decision is worth revisiting if Claude Code lets subagents ask questions, since the surveyor could then do more of the work.

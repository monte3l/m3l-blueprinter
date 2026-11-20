# Security Policy

## Scope

This covers `m3l-blueprinter`: a Claude Code plugin, planned and not built yet, with skills, one read-only agent and a command line tool that read a project's files and write design documents, decision records and diagrams into it, plus a planned npm package that ships the same command line tool. The main risk is input it will read and then act on: a policy pack fetched from an organization repository, and the contents of the project it documents.

## Supported versions

| Version                                                                      | Supported |
| ---------------------------------------------------------------------------- | --------- |
| The latest release, or the latest commit on `main` while there is no release | Yes       |
| Anything else                                                                | No        |

The monte3l maintainers look after it, so a fix lands on `main` and ships in the next release, and an older release is not patched.

## Reporting a vulnerability

Use GitHub's [private vulnerability reporting](https://github.com/monte3l/m3l-blueprinter/security/advisories/new) for this repository.

> [!IMPORTANT]
> Do not open a public issue for a suspected vulnerability.

Include the affected version, the steps to reproduce and the impact you think it has. You should get an acknowledgment within 7 days, and you will hear back before a fix ships.

## Response process

1. **Acknowledge** the report within 7 days.
2. **Triage.** Reproduce it, and decide its severity and scope.
3. **Fix privately** in a security advisory's temporary private fork. A confirmed vulnerability of medium or higher severity is fixed within 60 days of confirmation, and a critical one as fast as a review allows.
4. **Request a CVE** through the advisory when the issue warrants one.
5. **Publish** the advisory once the fix is released, crediting the reporter unless they ask not to be.

## What you can and cannot expect

**What this project guarantees.** These are the design's properties, and each becomes a test in the first milestone, so this list is a target until then:

- **A policy pack is data.** It is checked against a schema, its text is length-capped, and nothing in it is followed as an instruction.
- **The check needs no network.** `m3l-blueprinter check` reads only the files in the repository, so a CI run is reproducible.
- **Writes stay in the documentation tree.** The skills write only under `docs/`, `.blueprinter/` and `.blueprinter.json`, plus the docs site's `docs.config.mjs` entries where a site exists, and only after the owner answers the interview.
- **An accepted decision record is never edited.** The check compares it with a recorded hash.

**What it does not guarantee.** It does not review the architecture it documents, it does not make the documentation correct, and it does not protect a project from a malicious policy pack that its owner chose to trust. The surveyor agent reads the code but cannot run it.

## Dependency and code-scanning findings

Dependency Review fails a pull request that adds a dependency with a high or critical known vulnerability or a license outside the allow-list. CodeQL fails it on a new alert of error severity or critical or high security severity. Gitleaks scans for secrets.

## Accepted OpenSSF Scorecard findings

Background, not policy: this explains the Scorecard results for anyone who looks them up. `scorecard.yml` runs weekly and surfaces its findings as Code Scanning alerts. A new repository gets several on its first run (Maintained, Fuzzing and others it cannot satisfy yet). One that is dismissed instead of fixed is listed here with the reason, and the dismissal cites this section. Do not turn a dismissed finding into a standing bypass.

| Check    | Score | Why it stays |
| -------- | ----- | ------------ |
| None yet |       |              |

---
name: testfully-cli
description:
  Use when helping Testfully customers run API tests from the command line,
  configure Testfully CLI run options, generate reports, or check the installed
  CLI version.
license: MIT
metadata:
  author: testfully
  version: "1.0.0"
---

# Testfully CLI Skill

Use this skill when helping customers use Testfully CLI.

## When to Apply

Use and reference this skill and guidelines when:

- Running Testfully CLI against exported Testfully data, Postman collections, or
  TFML workspaces.
- Selecting or explaining `testfully run` options.
- Checking which Testfully CLI version is installed.
- Troubleshooting customer command examples.

## Commands

Testfully CLI supports running requests and checking the installed version. The
`run` command is also the default command, so `testfully <input>` runs the same
workflow as `testfully run <input>`.

- `run`: Execute one or more requests from supported input paths. Read
  [references/run-command.md](references/run-command.md) before using or
  documenting its options.
- `--version`: Print the installed CLI version. Read
  [references/version-command.md](references/version-command.md) before
  documenting version behavior.

## Usage Guidance

Use `--` before positional input paths when an option accepts one or more values
and appears immediately before the input file. This is especially useful with
options such as `--globals`, `--iteration-data`, and SSL certificate options.
Example: `testfully run --globals globals.toml -- collection`.

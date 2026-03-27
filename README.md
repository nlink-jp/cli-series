# cli-series

A collection of service-specific CLI clients maintained under the [nlink-jp](https://github.com/nlink-jp) organisation.

Each tool is a standalone project with its own repository, release cycle, and documentation.
This umbrella repository tracks them together as git submodules and hosts shared conventions.

## Tools

| Tool | Service | Language | Description |
|------|---------|----------|-------------|
| [scli](https://github.com/nlink-jp/scli) | Slack | Go | Terminal-based Slack client — read channels, post messages, DMs, search |
| [splunk-cli](https://github.com/nlink-jp/splunk-cli) | Splunk | Go | CLI client for the Splunk REST API — run searches, poll jobs, fetch results |
| [confl-cli](https://github.com/nlink-jp/confl-cli) | Confluence | Python | Confluence Cloud CLI — list, search, read, and export pages |

## Design Philosophy

- **Pipe-friendly**: stdout is data, stderr is progress and diagnostics.
- **Unix composable**: output in plain text or JSON; plays well with `jq`, `grep`, `xargs`.
- **User-operated**: tools authenticate as a human user, not a bot or service account.
- **Minimal dependencies**: ship as a single binary (Go) or a standalone uv-installable package (Python).

## Shared Conventions

See [CONVENTIONS.md](CONVENTIONS.md) for coding, documentation, and release standards that apply across all tools in this series.

## Adding a New Tool

1. Create the repository under `nlink-jp/`.
2. Follow [CONVENTIONS.md](CONVENTIONS.md) from the start.
3. Add it as a submodule here: `git submodule add git@github.com:nlink-jp/<tool>.git`
4. Add a row to the table above.

# AGENTS.md — cli-series

## Project summary

Umbrella repository for nlink-jp's interactive CLI clients for external
services (user-authenticated). Each tool lives in its own repository,
included here as a submodule. The catalog — one row per tool — is
[README.md](README.md); this file covers only how to work with the umbrella
(ADR-005). Series-specific design rules live in
[CONVENTIONS.md](CONVENTIONS.md).

## Key commands

| Command | Purpose |
|---------|---------|
| `git clone --recurse-submodules https://github.com/nlink-jp/cli-series.git` | Clone with all tools |
| `git submodule update --init` | Populate submodules in an existing clone |
| `git submodule update --remote <tool>` | Pull a tool's latest main |
| `git add <tool>` → commit `chore: bump <tool> to vX.Y.Z` | Update the pointer after a tool release |

## Gotchas

- Tool development happens in the tool repositories; new projects start in
  the workspace root `_wip/`, never directly inside this umbrella
  (CONVENTIONS.md — Starting a New Project).
- Submodule checkouts default to detached HEAD — `git checkout main` inside
  a submodule before committing.
- Submodule URLs are HTTPS only (SSH fails on machines without key auth).
- Every submodule needs a catalog row in README.md — `check-org.sh` fails
  otherwise. Keep the org profile README and nlink-web-site in sync when a
  tool's description changes.

## Module path

Repository: `github.com/nlink-jp/cli-series`

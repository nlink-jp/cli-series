# CLAUDE.md — cli-series

**Organization rules (mandatory): https://github.com/nlink-jp/.github/blob/main/CONVENTIONS.md**

## Non-negotiable rules

- **Tests are mandatory** — write them with the implementation. A feature is not complete without tests.
- **Design for testability** — pure functions, injected dependencies, no untestable globals.
- **Never `go build` directly** — always use `make build` (outputs to `dist/`). `go build` without `-o dist/...` drops the binary in the project root, polluting the working tree.
- **Docs in sync** — update `README.md` and `README.ja.md` in the same commit as behaviour changes.
- **Small, typed commits** — `feat:`, `fix:`, `test:`, `chore:`, `docs:`, `refactor:`, `security:`

## This series

Pipe-friendly, Unix-composable CLI clients for external services.
Authenticate as the human user, not a bot.

The catalog — one row per submodule — is [README.md](README.md) (ADR-005);
do not duplicate it here. A second list is a list that drifts: `check-org.sh`
holds the README to the submodules and nothing held this file, which had
fallen behind by the time anyone compared them.

Per-tool build quirks:

- **Python/uv (no make):** confl-cli
- **macOS-only (sandbox-exec), darwin/arm64 asset only:** gem-agent, lagent

## Release checklist

1. Update `CHANGELOG.md` → commit `chore: release vX.Y.Z` → tag → push
2. `gh release create` (no assets)
3. Build 4 platforms: `linux/amd64`, `linux/arm64`, `darwin/arm64`, `windows/amd64` (darwin is arm64-only)
4. Zip each binary + `README.md` → upload one by one
5. Update umbrella submodule pointer in this repo
6. Update org profile: `nlink-jp/.github/profile/README.md`

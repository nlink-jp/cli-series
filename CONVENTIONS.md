# cli-series Conventions

This document defines the conventions shared across all projects in the cli-series.
New projects must follow these conventions from the start.

Each project also maintains its own `CLAUDE.md` for project-specific instructions.

---

## Design Principles

### Pipe-friendly I/O

- **stdout**: structured data only (plain text, JSON, JSONL).
- **stderr**: progress messages, warnings, errors, diagnostics.
- Exit 0 on success, non-zero on failure. Never write errors to stdout.
- `--json` flag for machine-readable output where applicable.

### Composability

- Output must compose naturally with `jq`, `grep`, `xargs`, and redirection.
- Avoid interactive prompts in the default mode; use flags for anything requiring user input.
- `--no-color` (or respect `NO_COLOR` env var) to disable ANSI codes in piped contexts.

### User authentication, not bot authentication

- Tools authenticate as the human user who is running them.
- Never bundle hardcoded credentials or default tokens.
- Store credentials in the OS keychain, environment variables, or a local config file —
  in that priority order. See [Authentication](#authentication).

---

## Authentication

Credentials must never be stored in source code. Supported mechanisms in priority order:

1. **OS keychain** (recommended for interactive use): store tokens via system keyring APIs.
2. **Environment variables**: `<SERVICE>_TOKEN`, `<SERVICE>_API_TOKEN`, etc.
3. **`.env` file** in the working directory (loaded at startup, `.gitignore`d).
4. **Config file** at `~/.config/<tool>/config.{toml,yaml,json}` (sensitive fields only).

Config files that may contain secrets must warn on insecure permissions
(`perm & 0077 != 0`):

```
Warning: config file <path> has permissions <octal>; expected 0600.
  The file may contain credentials. Run: chmod 600 <path>
```

---

## Project Structure

### Go projects

```
<tool>/
├── cmd/              # CLI entry point (Cobra commands)
├── internal/         # Application-private packages
├── docs/
│   └── ja/           # Japanese translations
├── scripts/
│   └── hooks/        # pre-commit, pre-push
├── CHANGELOG.md
├── CLAUDE.md
├── Makefile
├── README.md
└── README.ja.md      # (if maintained)
```

### Python projects

```
<tool>/
├── src/<package>/    # Application source
├── tests/
├── docs/
│   └── ja/
├── CHANGELOG.md
├── CLAUDE.md
├── pyproject.toml
├── README.md
└── README.ja.md      # (if maintained)
```

---

## CLI Conventions

- Use [Cobra](https://github.com/spf13/cobra) (Go) or [Click](https://click.palletsprojects.com/) / [Typer](https://typer.tiangolo.com/) (Python).
- Long flags use hyphens: `--page-id`, `--no-color`, `--output-format`.
- Short flags for common options: `-o` (output), `-q` (quiet), `-v` (verbose).
- `--version` / `-V` prints `<tool> vX.Y.Z`.
- `--help` / `-h` prints usage.

---

## Makefile Targets (Go projects)

| Target | Description |
|--------|-------------|
| `build` | Compile binary for current platform |
| `test` | Run all tests |
| `vet` | Run `go vet ./...` |
| `lint` | Run `golangci-lint run ./...` |
| `check` | Full quality gate: vet → lint → test → build |
| `build-all` | Cross-compile for all target platforms |
| `clean` | Remove build artifacts |

Version injection:

```makefile
VERSION ?= $(shell git describe --tags --always --dirty 2>/dev/null || echo "dev")
LDFLAGS := -ldflags "-X main.version=$(VERSION)"
```

### Cross-compilation targets (Go)

| GOOS | GOARCH |
|------|--------|
| linux | amd64 |
| linux | arm64 |
| darwin | amd64 |
| darwin | arm64 |
| windows | amd64 |

---

## Release Conventions (Python with `uv`)

| Target | Command |
|--------|---------|
| Test | `uv run pytest` |
| Lint | `uv run ruff check` |
| Type check | `uv run mypy src/` |
| Build | `uv build` (produces `dist/*.tar.gz` and `dist/*.whl`) |

---

## Release Process

1. All tests pass (`make check` for Go; `uv run pytest` + `uv run ruff check` for Python).
2. Update `CHANGELOG.md` with the new version and date.
3. Update any stale documentation (`docs/`, `README.md`, `README.ja.md`).
   Japanese translations must be kept in sync with every English change.
4. Commit and push to `main`.
5. Create a Git tag (`git tag vX.Y.Z`) and push it.
6. **Go**: `make build-all` → zip each binary → create GitHub Release → upload zips.
   **Python**: `uv build` → create GitHub Release → upload `.tar.gz` and `.whl` as assets.
7. Update the `cli-series` umbrella repository:
   `git submodule update --remote <tool>` → commit the updated submodule pointer.

Breaking changes require a **minor version bump** while the project is in the `0.x` series.

---

## CHANGELOG and Versioning

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) +
[Semantic Versioning](https://semver.org/).

Section categories: `Added`, `Changed`, `Fixed`, `Removed`, `Security`, `Docs`, `Internal`.
Prefix breaking changes with **Breaking:**.

---

## Documentation Conventions

- Primary documentation in **English**.
- Japanese translation (`README.ja.md`, `docs/ja/`) maintained in parallel where applicable.
- `README.md` sections (in order): description → features → installation → configuration →
  usage → building → documentation links.
- Stale documentation is a bug.

---

## Security

- Config files with credentials must check permissions on load (see [Authentication](#authentication)).
- Tools that transmit credentials over HTTP (not HTTPS) must warn on stderr.
- Dependencies must be kept up to date; run `govulncheck` (Go) or `uv audit` (Python)
  as part of the quality gate.

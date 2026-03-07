# create-rust-project

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) that interactively scaffolds new Rust projects.

## Installation

In Claude Code, run:

```
/plugin sshine/create-rust-project
```

Or via npx:

```sh
npx skills add sshine/create-rust-project
```

This installs the `/new-rust-project` skill into your Claude Code environment.

To install globally (available in all projects):

```sh
npx skills add -g sshine/create-rust-project
```

## Usage

In any directory, open Claude Code and run:

```
/new-rust-project my-project
```

Or describe what you want naturally:

> I want to create a new Rust project called my-project

The skill will ask about:

1. **Project name** — validated lowercase + hyphens
2. **Project type** — workspace, binary, or library
3. **Short description** — for Cargo.toml and README
4. **Nix integration** — crate2nix flake (recommended), simple shell.nix, or none
5. **Error handling** — thiserror (generates `src/error.rs`) or anyhow
6. **Dev dependencies** — insta, proptest, criterion, clap, tokio
7. **CI** — GitHub Actions (Nix-based), Lefthook
8. **License** — MIT, Apache-2.0, LGPL 3.0

Then generates all project files and optionally runs `git init` and `nix flake lock`.

## Generated project features

- Cargo workspace with `crates/` layout (or single-crate)
- Edition 2024, strict lints (`unused_must_use = "deny"`, `unwrap_used = "deny"`)
- Always included: serde, serde_json, tracing
- Custom `Error` enum + `Result<T>` alias (when thiserror is chosen)
- `justfile` with fmt, lint, test, build, ci, watch recipes
- `rust-toolchain.toml` for reproducible toolchain
- Optional: `flake.nix` with crate2nix + rust-overlay + flake-parts + devshell
- Optional: Nix-based GitHub Actions CI
- Pre-configured `.claude/settings.json` and `CLAUDE.md`

## Design

Reference templates live in `.claude/skills/new-rust-project/references/`. Each template contains a documented pattern with `{{placeholder}}` syntax that the skill substitutes based on user answers.

This skill prototype validates the question flow and template content before building a standalone Rust CLI tool.

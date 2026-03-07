# create-rust-project

Shareable Claude Code skill that interactively scaffolds new Rust projects.

## Structure

- `.claude/skills/new-rust-project/SKILL.md` — skill definition with question flow and generation logic
- `.claude/skills/new-rust-project/references/` — template files for each generated component
- This file (`CLAUDE.md`) — meta-instructions for working on the template itself

## How it works

The `/new-rust-project` skill asks questions (project type, error handling, Nix integration, dev dependencies, CI, license), then generates all files for a new Rust project. Reference templates in `references/` use `{{placeholder}}` syntax that the skill substitutes at generation time.

## Reference files

| File | Generates |
|------|-----------|
| `cargo-workspace.md` | Root `Cargo.toml` for workspaces |
| `cargo-single.md` | `Cargo.toml` for binary/library projects |
| `cargo-crate.md` | Per-crate `Cargo.toml` in workspaces |
| `error-rs.md` | `src/error.rs` with `Error` enum and `Result<T>` (when thiserror is chosen) |
| `justfile.md` | `justfile` with dev recipes |
| `ci-github-actions.md` | `.github/workflows/rust.yml` (Nix-based CI) |
| `flake-crate2nix.md` | `flake.nix` + flake-parts modules |
| `shell-nix-simple.md` | Simple `shell.nix` with rustup |
| `claude-md.md` | `CLAUDE.md` for generated projects |
| `gitignore.md` | `.gitignore` |
| `claude-settings.md` | `.claude/settings.json` |

## Conventions

- Templates use `{{placeholder}}` for simple substitution and `{{#if}}`/`{{#each}}` for conditional/repeated sections
- All generated projects use edition 2024 and strict lints (`unused_must_use = "deny"`, clippy `unwrap_used = "deny"`)
- Always-included deps: serde/serde_json, tracing, and either thiserror or anyhow
- The Nix integration uses crate2nix + rust-overlay via flake-parts (modern approach with per-crate granularity)

## Distribution

This skill is installable via `npx skills add`:

```sh
npx skills add sshine/create-rust-project
```

The `name` field in SKILL.md must match the directory name (`new-rust-project`).

## Future

This prototype informs a standalone Rust CLI tool using `clap` + `dialoguer` + `tera`/`minijinja`. Reference files become embedded Tera templates.

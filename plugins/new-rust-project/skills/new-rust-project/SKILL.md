---
name: new-rust-project
description: >
  Scaffold a new Rust project. Use when the user wants to create a new Rust
  project, crate, or workspace. Asks about project type, Nix integration,
  error handling, dependencies, CI, and license, then generates all files.
argument-hint: [project-name]
allowed-tools: Bash, Read, Write, Edit, Glob, AskUserQuestion
---

# Rust Project Scaffolding Skill

You are scaffolding a new Rust project. Follow these steps exactly.

## Step 1: Ask Questions

Ask the following questions **sequentially** using `AskUserQuestion`. Do not ask all at once.

### Q1: Project Name

Ask for the project name. Default: `$ARGUMENTS` if provided, otherwise the current directory name.
Validate: must be lowercase ASCII letters, digits, and hyphens only. Must start with a letter.

### Q2: Project Type

Options:
- **Workspace** (recommended) — multi-crate workspace with `crates/` directory
- **Binary** — single crate with `src/main.rs`
- **Library** — single crate with `src/lib.rs`

Default: Workspace

If workspace is chosen, ask a follow-up:

#### Q2b: Initial Crate Names

Suggest `<name>-core` and `<name>-cli` as defaults. Allow the user to customize.
Each crate name must follow the same naming rules as the project name.
Ask which crates should be libraries vs binaries (default: all libraries except `*-cli` which is binary).

### Q3: Short Description

Ask for a one-line description for `Cargo.toml` and `README.md`.

### Q4: Nix Integration

Options:
- **Full flake.nix** with flake-parts, treefmt-nix, crate2nix, rust-overlay (recommended)
- **Simple shell.nix** — rustup-based, minimal Nix support
- **None** — no Nix files

Default: Full flake.nix

If flake is chosen, there should be a .envrc file containing "use flake".

If simple Nix is chosen, there should be a .envrc file containing "use nix".

### Q5: Error Handling

Options:
- **thiserror** (recommended) — derive macro for custom error enums; generates `src/error.rs` with `Error` enum and `Result<T>` alias
- **anyhow** — convenient boxed errors for applications; no `src/error.rs` generated

Default: thiserror

### Q6: Dev Dependencies (multi-select)

The following are always included (do not ask):
- `serde` (+ `serde_json`) — serialization
- `tracing` — structured diagnostics

Options to ask about:
- **insta** — snapshot testing (default: selected)
- **proptest** — property-based testing
- **criterion** — benchmarking
- **clap** — CLI argument parsing (suggest for binary crates)
- **tokio** — async runtime

### Q7: CI (multi-select)

Options:
- **GitHub Actions** (default: selected)
- **Lefthook** (default: selected)

### Q8: License (multi-select)

Options:
- **MIT** (default: selected)
- **Apache-2.0**
- **LGPL 3.0**

If multiple licenses are selected, provide multiple aptly named license files.

### Q9: GitHub Username

Default: Current system user.

## Step 2: Confirm

Print a summary of all choices in a clear table/list format. Ask the user to confirm before generating files.

## Step 3: Generate Files

Generate all files based on the user's answers. Use the reference templates in `references/` as the basis for each file. Read each relevant reference file and adapt its template content using the user's answers.

### Files to generate for ALL project types:

| File | Reference | Notes |
|------|-----------|-------|
| `Cargo.toml` | `cargo-workspace.md` or `cargo-single.md` | Depends on project type |
| `rust-toolchain.toml` | (inline) | Always: `channel = "stable"` |
| `justfile` | `justfile.md` | Adapt recipes based on testing extras |
| `.gitignore` | `gitignore.md` | Include `Cargo.lock` for libraries only |
| `CLAUDE.md` | `claude-md.md` | Adapt to project structure |
| `.claude/settings.json` | `claude-settings.md` | Adapt allowed tools based on Nix choice |
| `README.md` | (inline) | Title, description, badges |

### Conditional files:

| Condition | File | Reference |
|-----------|------|-----------|
| Workspace | `crates/<name>/Cargo.toml` | `cargo-crate.md` |
| Workspace | `crates/<name>/src/lib.rs` or `main.rs` | (inline) |
| Binary | `src/main.rs` | (inline) |
| Library | `src/lib.rs` | (inline) |
| thiserror chosen | `src/error.rs` (single crate) or `crates/<core>/src/error.rs` (workspace) | `error-rs.md` |
| Nix: full flake | `flake.nix` | `flake-crate2nix.md` |
| Nix: full flake | `nix/devshell.nix` | `flake-crate2nix.md` |
| Nix: full flake | `nix/rust-overlay.nix` | `flake-crate2nix.md` |
| Nix: full flake | `nix/package-overlay.nix` | `flake-crate2nix.md` |
| Nix: full flake | `.envrc` | `"use flake"` |
| Nix: simple | `shell.nix` | `shell-nix-simple.md` |
| Nix: simple | `.envrc` | `"use nix"` |
| CI: GitHub Actions | `.github/workflows/rust.yml` | `ci-github-actions.md` |
| License: MIT | `LICENSE` | Generate MIT text |
| License: Apache-2.0 | `LICENSE` | Generate Apache text |
| License: LGPL-2.0 | `LICENSE` | Generate LGPL text |

### Inline templates:

**rust-toolchain.toml:**
```toml
[toolchain]
channel = "stable"
components = ["rustfmt", "clippy"]
```

**src/main.rs:**
```rust
fn main() {
    println!("Hello, world!");
}
```

**src/lib.rs:**
```rust
//! {{description}}
```

**README.md:**
```markdown
# {{project-name}}

{{description}}
```

## Step 4: Post-Generation

After generating all files, offer to:
1. Run `git init` but don't create any commits
2. If flake.nix was generated, run `nix flake lock` to create `flake.lock`

## Important Notes

- The generated workspace `Cargo.toml` file uses `edition = "2024"`
- Inherit properties from workspace `Cargo.toml` in sub-crates except for names, versions
- Always include workspace lints: `unused_must_use = "deny"` and clippy `unwrap_used = "deny"`
- For workspaces, all dependencies shared across crates go in `[workspace.dependencies]`
- The `rust-toolchain.toml` is always generated (even without Nix) for reproducibility
- When generating files, create all parent directories as needed with `mkdir -p`

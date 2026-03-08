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

**You MUST call the `AskUserQuestion` tool for EACH question below. Do NOT skip questions or assume answers. Wait for the user's response before proceeding to the next question.**

Before starting, detect the current directory name and run `git config user.name` to get the GitHub username default.

---

### Q1: Project Name — `header: "Name"`

Determine the default name: use `$ARGUMENTS` if provided and valid (lowercase ASCII letters, digits, hyphens; must start with a letter), otherwise use the current directory name.

Call `AskUserQuestion` with:
- `header`: `"Name"`
- `question`: `"What should the project be called?"`
- `multiSelect`: `false`
- `options`:
  - If `$ARGUMENTS` is provided and valid:
    - label: `"Use \"<argument>\""`, description: `"From command arguments"`
    - label: `"Use \"<dirname>\""`, description: `"From current directory name"`
  - If `$ARGUMENTS` is not provided or invalid:
    - label: `"Use \"<dirname>\""`, description: `"From current directory name"`
    - label: `"Enter custom name"`, description: `"Type a project name (lowercase, hyphens ok)"`

The user may also select "Other" to type a custom name. Validate: lowercase ASCII letters, digits, hyphens; must start with a letter.

**STOP and WAIT for the user's response before continuing.**

---

### Q2: Project Type — `header: "Type"`

Call `AskUserQuestion` with:
- `header`: `"Type"`
- `question`: `"What type of Rust project?"`
- `multiSelect`: `false`
- `options`:
  - label: `"Workspace (Recommended)"`, description: `"Multi-crate workspace with crates/ directory"`
  - label: `"Binary"`, description: `"Single crate with src/main.rs"`
  - label: `"Library"`, description: `"Single crate with src/lib.rs"`

**STOP and WAIT for the user's response before continuing.**

---

### Q2b: Initial Crate Names (only if Workspace was chosen) — `header: "Crates"`

Compute defaults: `<name>-core` (library) and `<name>-cli` (binary).

Call `AskUserQuestion` with:
- `header`: `"Crates"`
- `question`: `"Which crates should the workspace contain?"`
- `multiSelect`: `false`
- `options`:
  - label: `"<name>-core + <name>-cli"`, description: `"Core library and CLI binary (default)"`
  - label: `"<name>-core only"`, description: `"Just the core library crate"`
  - label: `"<name>-cli only"`, description: `"Just the CLI binary crate"`

The user may select "Other" to specify custom crate names (comma-separated). Each crate name must follow the same naming rules as the project name. Crates ending in `-cli` are binaries; all others are libraries.

**STOP and WAIT for the user's response before continuing.**

---

### Q3: Short Description — `header: "Description"`

Compute a default description based on project type, e.g. `"A Rust workspace"`, `"A Rust CLI application"`, or `"A Rust library"`.

Call `AskUserQuestion` with:
- `header`: `"Description"`
- `question`: `"One-line project description for Cargo.toml and README?"`
- `multiSelect`: `false`
- `options`:
  - label: `"<default description>"`, description: `"Auto-generated based on project type"`
  - label: `"Enter custom description"`, description: `"Type your own description"`

The user may select "Other" to type a custom description.

**STOP and WAIT for the user's response before continuing.**

---

### Q4: Nix Integration — `header: "Nix"`

Call `AskUserQuestion` with:
- `header`: `"Nix"`
- `question`: `"What level of Nix integration?"`
- `multiSelect`: `false`
- `options`:
  - label: `"Full flake.nix (Recommended)"`, description: `"flake-parts, treefmt-nix, crate2nix, rust-overlay"`
  - label: `"Simple shell.nix"`, description: `"Minimal rustup-based Nix shell"`
  - label: `"None"`, description: `"No Nix files"`

If flake is chosen, generate a `.envrc` file containing `use flake`.
If simple Nix is chosen, generate a `.envrc` file containing `use nix`.

**STOP and WAIT for the user's response before continuing.**

---

### Q5: Error Handling — `header: "Errors"`

Call `AskUserQuestion` with:
- `header`: `"Errors"`
- `question`: `"Which error handling approach?"`
- `multiSelect`: `false`
- `options`:
  - label: `"thiserror (Recommended)"`, description: `"Derive macro for custom error enums; generates src/error.rs"`
  - label: `"anyhow"`, description: `"Convenient boxed errors for applications; no error.rs"`

**STOP and WAIT for the user's response before continuing.**

---

### Q6: Extra Dependencies — `header: "Extras"`, `multiSelect: true`

The following are always included (do not ask): `serde` (+ `serde_json`), `tracing`.

Call `AskUserQuestion` with:
- `header`: `"Extras"`
- `question`: `"Which additional dependencies do you want? (serde, tracing always included)"`
- `multiSelect`: `true`
- `options`:
  - label: `"insta"`, description: `"Snapshot testing"`
  - label: `"proptest"`, description: `"Property-based testing"`
  - label: `"criterion"`, description: `"Benchmarking"`
  - label: `"clap"`, description: `"CLI argument parsing"`
  - label: `"tokio"`, description: `"Async runtime"`

**STOP and WAIT for the user's response before continuing.**

---

### Q7 + Q8: CI and License — asked together in one `AskUserQuestion` call

Call `AskUserQuestion` with **two questions** in a single call:

**Question 1:**
- `header`: `"CI"`
- `question`: `"Which CI/tooling integrations?"`
- `multiSelect`: `true`
- `options`:
  - label: `"GitHub Actions"`, description: `"CI workflow in .github/workflows/"`
  - label: `"Lefthook"`, description: `"Git hooks manager for pre-commit checks"`

**Question 2:**
- `header`: `"License"`
- `question`: `"Which license(s)?"`
- `multiSelect`: `true`
- `options`:
  - label: `"MIT"`, description: `"Permissive, simple"`
  - label: `"Apache-2.0"`, description: `"Permissive with patent grant"`
  - label: `"LGPL-3.0"`, description: `"Copyleft for libraries"`

If multiple licenses are selected, provide multiple aptly named license files.

**STOP and WAIT for the user's response before continuing.**

---

### Q9: GitHub Username — `header: "GitHub"`

Use the value from `git config user.name` (detected earlier) as the default. If unavailable, fall back to the system username.

Call `AskUserQuestion` with:
- `header`: `"GitHub"`
- `question`: `"What is your GitHub username (for CI and README badges)?"`
- `multiSelect`: `false`
- `options`:
  - label: `"Use \"<detected-name>\""`, description: `"Detected from git config"`
  - label: `"Enter different username"`, description: `"Type your GitHub username"`

The user may select "Other" to type a custom username.

**STOP and WAIT for the user's response before continuing.**

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

# Cargo.toml — Workspace Crate

Generate a per-crate `Cargo.toml` at `crates/<crate-name>/Cargo.toml`. These inherit most fields from the workspace.

## Library crate template

```toml
[package]
name = "{{crate-name}}"
version.workspace = true
edition.workspace = true
license.workspace = true
repository.workspace = true
description = "{{crate-description}}"

[lints]
workspace = true

[dependencies]
{{#if thiserror}}
thiserror.workspace = true
{{/if}}
{{#if anyhow}}
anyhow.workspace = true
{{/if}}
serde.workspace = true
tracing.workspace = true

[dev-dependencies]
{{#if insta}}
insta.workspace = true
{{/if}}
{{#if proptest}}
proptest.workspace = true
{{/if}}
```

## Binary crate template

```toml
[package]
name = "{{crate-name}}"
version.workspace = true
edition.workspace = true
license.workspace = true
repository.workspace = true
description = "{{crate-description}}"

[lints]
workspace = true

[dependencies]
{{#each lib-crates}}
{{this.name}}.workspace = true
{{/each}}
{{#if thiserror}}
thiserror.workspace = true
{{/if}}
{{#if anyhow}}
anyhow.workspace = true
{{/if}}
serde.workspace = true
tracing.workspace = true
{{#if clap}}
clap.workspace = true
{{/if}}
{{#if tokio}}
tokio.workspace = true
{{/if}}
```

## Notes

- All metadata fields use `.workspace = true` to inherit from the workspace manifest
- `[lints] workspace = true` inherits the workspace lint configuration
- Binary crates should depend on library crates from the same workspace
- Only add workspace dependencies that the crate actually uses
- The `description` field is crate-specific and should NOT use `.workspace = true`
- Include either `thiserror` or `anyhow` based on the error handling choice, plus `serde` and `tracing`
- `clap` and `tokio` go on binary crates only when selected
- Add testing dependencies only when the corresponding testing extra was selected

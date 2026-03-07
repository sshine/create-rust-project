# Cargo.toml — Workspace Manifest

Generate a workspace `Cargo.toml` using this template. Replace `{{placeholders}}` with actual values.

```toml
[workspace]
resolver = "2"
members = ["crates/*"]

[workspace.package]
version = "0.1.0"
edition = "2024"
license = "{{license}}"
repository = "https://github.com/{{github-username}}/{{project-name}}"

[workspace.lints.rust]
unused_must_use = "deny"

[workspace.lints.clippy]
unwrap_used = "deny"

[workspace.dependencies]
# Internal crates
{{#each crates}}
{{this.name}} = { path = "crates/{{this.name}}" }
{{/each}}

# Error handling
{{#if thiserror}}
thiserror = "2"
{{/if}}
{{#if anyhow}}
anyhow = "1"
{{/if}}

# Serialization
serde = { version = "1", features = ["derive"] }
serde_json = "1"

# Diagnostics
tracing = "0.1"

{{#if insta}}
# Dev dependencies
insta = { version = "1", features = ["yaml"] }
{{/if}}
{{#if proptest}}
proptest = "1"
{{/if}}
{{#if criterion}}
criterion = { version = "0.5", features = ["html_reports"] }
{{/if}}
{{#if clap}}

# CLI
clap = { version = "4", features = ["derive"] }
{{/if}}
{{#if tokio}}

# Async
tokio = { version = "1", features = ["full"] }
{{/if}}
```

## Notes

- `resolver = "2"` is required for workspaces (and is the default for edition 2024, but explicit is better)
- `members = ["crates/*"]` auto-discovers all crates in the `crates/` directory
- All shared dependencies go in `[workspace.dependencies]` so crates can use `.workspace = true`
- Either `thiserror` or `anyhow` is included based on the error handling choice
- `serde`/`serde_json` and `tracing` are always included as shared dependencies
- `clap` and `tokio` are added only when selected
- Testing dependencies (insta, proptest, criterion) are added only when selected
- The `license` field should use SPDX identifiers: `MIT`, `Apache-2.0`, or `MIT OR Apache-2.0`
- If no license is chosen, omit the `license` field entirely

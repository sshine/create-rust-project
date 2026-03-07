# Cargo.toml — Single Crate (Binary or Library)

Generate a single-crate `Cargo.toml` using this template. Replace `{{placeholders}}` with actual values.

```toml
[package]
name = "{{project-name}}"
version = "0.1.0"
edition = "2024"
description = "{{description}}"
license = "{{license}}"
repository = "https://github.com/{{github-username}}/{{project-name}}"

[lints.rust]
unused_must_use = "deny"

[lints.clippy]
unwrap_used = "deny"

[dependencies]
{{#if thiserror}}
thiserror = "2"
{{/if}}
{{#if anyhow}}
anyhow = "1"
{{/if}}
serde = { version = "1", features = ["derive"] }
serde_json = "1"
tracing = "0.1"
{{#if clap}}
clap = { version = "4", features = ["derive"] }
{{/if}}
{{#if tokio}}
tokio = { version = "1", features = ["full"] }
{{/if}}

[dev-dependencies]
{{#if insta}}
insta = { version = "1", features = ["yaml"] }
{{/if}}
{{#if proptest}}
proptest = "1"
{{/if}}

{{#if criterion}}
[[bench]]
name = "benchmarks"
harness = false

[dev-dependencies.criterion]
version = "0.5"
features = ["html_reports"]
{{/if}}
```

## Notes

- Same lint rules as workspace: `unused_must_use = "deny"` and clippy `unwrap_used = "deny"`
- Either `thiserror` or `anyhow` is included based on the error handling choice
- `serde`/`serde_json` and `tracing` are always included
- Testing deps go in `[dev-dependencies]`
- For criterion benchmarks, add a `[[bench]]` section with `harness = false`
- If no license is chosen, omit the `license` field
- For libraries, also consider adding `keywords`, `categories`, and `readme` fields

# CLAUDE.md — Generated Project Instructions

Generate a `CLAUDE.md` for the new project. Adapt based on project structure, testing choices, and Nix integration.

## Template

```markdown
# {{project-name}}

{{description}}

## Architecture

{{#if workspace}}
This is a Cargo workspace with crates under `crates/`:

{{#each crates}}
- **{{this.name}}**: {{this.description}}
{{/each}}
{{else if binary}}
Single binary crate.
{{else}}
Single library crate.
{{/if}}

## Development

{{#if nix-flake}}
### Nix (recommended)

This project uses a Nix flake for reproducible development:

```sh
direnv allow    # auto-activates the dev shell
# or manually:
nix develop     # enter the dev shell
```
{{else if nix-shell}}
### Nix

```sh
direnv allow    # auto-activates the dev shell
# or manually:
nix-shell       # enter the dev shell
```
{{/if}}

### Common commands

```sh
just            # list all available commands
just fmt        # format code
just lint       # run clippy
just test       # run all tests
just ci         # run all CI checks locally
```

{{#if insta}}
### Snapshot testing

Uses [insta](https://insta.rs/) for snapshot tests:

```sh
just snap       # run tests and review snapshot changes
```
{{/if}}

{{#if criterion}}
### Benchmarks

```sh
just bench      # run benchmarks
```
{{/if}}

## Conventions

- Use `thiserror` for error types; avoid `unwrap()` (denied by clippy lint)
- All public items should have doc comments
- Run `just ci` before pushing
{{#if insta}}
- Use insta snapshots for parser output and complex data structures
{{/if}}
```

## Notes

- Keep the CLAUDE.md focused and concise — it's consumed by Claude Code agents
- Architecture section should accurately describe the crate layout
- Development section should match the chosen Nix integration
- Conventions section encodes the project's lint rules in human terms
- Only include sections relevant to the chosen configuration

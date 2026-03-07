# .claude/settings.json

Generate `.claude/settings.json` with pre-approved tool permissions.

## Base template (always included)

```json
{
  "permissions": {
    "allow": [
      "Bash(git commit:*)",
      "Bash(git log:*)",
      "Bash(git diff:*)",
      "Bash(git status:*)",
      "Bash(git add:*)",
      "Bash(cargo:*)",
      "Bash(just:*)"
    ]
  }
}
```

## Conditional additions to the `allow` array

### If Nix flake integration is chosen:

```json
"Bash(nix flake:*)",
"Bash(nix build:*)",
"Bash(nix eval:*)",
"Bash(nix fmt:*)",
"Bash(nix develop:*)"
```

### If Nix shell integration is chosen:

```json
"Bash(nix-shell:*)"
```

## Notes

- Pre-approving common commands reduces friction when Claude Code runs builds and tests
- `cargo:*` covers `cargo build`, `cargo test`, `cargo clippy`, `cargo fmt`, etc.
- `just:*` covers all justfile recipes
- Git commands are pre-approved for read operations and commits (not push)
- Nix commands are only added when Nix integration is selected
- Do NOT pre-approve destructive operations like `git push`, `git reset`, `rm -rf`

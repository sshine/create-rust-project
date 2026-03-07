# .gitignore

Generate a `.gitignore` adapted to the project configuration.

## Template

```gitignore
# Rust
/target/

# IDE
.idea/
.vscode/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Editor backups
*~
\#*\#
```

## Conditional additions

### If project type is library (single crate):

Add to the Rust section:
```gitignore
# Lock file not committed for libraries (see https://doc.rust-lang.org/cargo/faq.html#why-have-cargolock-in-version-control)
Cargo.lock
```

### If Nix integration is enabled (either flake or shell.nix):

Add a Nix section:
```gitignore
# Nix
/.direnv/
/result
```

## Notes

- `Cargo.lock` is committed for binaries and workspaces, but NOT for standalone libraries
- `/target/` covers the build directory for both workspace root and single crates
- `/.direnv/` is the direnv cache directory (only relevant with Nix + direnv)
- `/result` is the Nix build output symlink (only relevant with flake.nix)
- IDE directories are excluded by default for clean repos

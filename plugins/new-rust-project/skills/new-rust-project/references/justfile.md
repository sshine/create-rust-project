# justfile

Generate a `justfile` with common development recipes. Adapt based on selected testing extras.

## Base template (always included)

```justfile
# Default recipe: list available commands
default:
    @just --list

# Format all code
fmt:
    cargo fmt --all

# Check formatting
fmt-check:
    cargo fmt --all -- --check

# Run clippy lints
lint:
    cargo clippy --all-targets --all-features -- -D warnings

# Run all tests
test:
    cargo test --all-features

# Run tests with verbose output
test-verbose:
    cargo test --all-features -- --nocapture

# Build release
build:
    cargo build --release --all-features

# Generate and open documentation
doc:
    cargo doc --no-deps --all-features --open

# Run CI checks locally
ci: fmt-check lint test doc build
    @echo "All CI checks passed!"

# Watch for changes and run tests
watch:
    cargo watch -x test

# Clean build artifacts
clean:
    cargo clean
```

## Conditional recipes

### If insta is selected, add:

```justfile
# Review snapshot test changes
snap:
    cargo insta test --review
```

### If criterion is selected, add:

```justfile
# Run benchmarks
bench:
    cargo bench --all-features
```

## Notes

- For single-crate projects, the recipes are the same (cargo commands work the same way)
- The `ci` recipe chains: `fmt-check lint test doc build` — add `bench` if criterion is selected
- `cargo watch` requires `cargo-watch` to be installed (include in shell.nix/devshell if using Nix)
- The `doc` recipe uses `--open` to open docs in a browser; CI should use `doc` without `--open`
- All recipes use `--all-features` to ensure comprehensive checking

# GitHub Actions CI (Nix-based)

Generate `.github/workflows/rust.yml` for Nix-based Rust CI. All build steps run inside the Nix dev shell via `nix develop -c`.

```yaml
name: Rust CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      CARGO_TERM_COLOR: always

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Install Nix
        uses: nixbuild/nix-quick-install-action@v34
        with:
          nix_conf: |
            keep-env-derivations = true
            keep-outputs = true

      - name: Restore and save Nix store
        uses: nix-community/cache-nix-action@v7
        with:
          primary-key: nix-${{ runner.os }}-${{ hashFiles('**/*.nix', '**/flake.lock') }}
          restore-prefixes-first-match: nix-${{ runner.os }}-
          gc-max-store-size-linux: 2G
          purge: true
          purge-prefixes: nix-${{ runner.os }}-
          purge-created: 0
          purge-last-accessed: P1DT12H
          purge-primary-key: never

      - name: Build Nix dev shell
        run: nix develop -c true

      - name: Check formatting
        run: nix develop -c cargo fmt --all -- --check

      - name: Clippy
        run: nix develop -c cargo clippy --all-targets --all-features -- -D warnings

      - name: Test
        run: nix develop -c cargo test --all-features

      - name: Build
        run: nix develop -c cargo build --release --all-features

      - name: Documentation
        run: nix develop -c cargo doc --no-deps --all-features
```

## Notes

- Uses `nixbuild/nix-quick-install-action` to install Nix on the runner
- Uses `nix-community/cache-nix-action` to cache the Nix store between runs (much faster than re-downloading)
- All cargo commands run via `nix develop -c` so they use the exact same toolchain as local development
- No need for `dtolnay/rust-toolchain` or `Swatinem/rust-cache` — Nix handles both toolchain and caching
- `CARGO_TERM_COLOR: always` ensures colored output in CI logs
- The Nix store cache key includes both `*.nix` files and `flake.lock` for proper invalidation
- `gc-max-store-size-linux: 2G` limits cache size; adjust based on project needs
- Single job is simpler than parallel jobs since Nix caching makes rebuilds fast
- For workspaces and single crates, the cargo commands are identical
- If Nix integration is "None" or "Simple shell.nix", fall back to the non-Nix CI approach using `dtolnay/rust-toolchain@stable` and `Swatinem/rust-cache@v2` directly (without `nix develop -c`)

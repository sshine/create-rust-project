# shell.nix — Simple Rustup-Based

Generate a minimal `shell.nix` for development using rustup (not Nix-managed Rust).

## shell.nix

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
  buildInputs = [
    pkgs.just
    pkgs.rustup
    pkgs.cargo-watch
    {{#if insta}}
    pkgs.cargo-insta
    {{/if}}
  ];

  shellHook = ''
    export RUST_BACKTRACE=1
  '';
}
```

## .envrc

```
use nix
```

## Notes

- This is the simplest Nix setup: just provides tools, Rust itself comes from rustup
- The user must have rustup installed and a toolchain configured (`rust-toolchain.toml` handles this)
- `cargo-watch` is included for the `just watch` recipe
- `cargo-insta` is included only when insta snapshot testing is selected
- The `.envrc` uses `use nix` (not `use flake`) since there's no flake
- `RUST_BACKTRACE=1` is set for better error messages during development

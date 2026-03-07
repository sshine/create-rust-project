# src/error.rs — Custom Error Type

Generate `src/error.rs` when `thiserror` is chosen. Do NOT generate this file when `anyhow` is chosen.

For workspaces, place this in the core/library crate (e.g. `crates/<name>-core/src/error.rs`).

## Template

```rust
use thiserror::Error;

/// A convenient result type alias.
pub type Result<T> = std::result::Result<T, Error>;

/// Top-level error type for the project.
#[derive(Debug, Error)]
pub enum Error {
    #[error("I/O error: {0}")]
    Io(#[from] std::io::Error),

    #[error("parse error: {0}")]
    Parse(String),

    #[error("{0}")]
    Other(String),
}
```

## Integration

The crate's `lib.rs` or `main.rs` should re-export the error module:

```rust
pub mod error;
pub use error::{Error, Result};
```

For `main.rs` in binary crates, use it as:

```rust
mod error;
use error::Result;

fn main() -> Result<()> {
    Ok(())
}
```

## Notes

- The `Error` enum uses `thiserror::Error` derive for `Display` and `From` implementations
- `Result<T>` is an alias for `std::result::Result<T, Error>` — avoids importing both
- `Io` variant uses `#[from]` for automatic conversion from `std::io::Error`
- `Parse` and `Other` are generic starter variants — the user should replace/extend these with domain-specific errors as the project evolves
- Keep the initial enum small; it's a starting point, not an exhaustive list

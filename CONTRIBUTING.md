# Contributing

This repository owns the `crane-cli` library and `crane` executable. Make
implementation changes here, not in a copied workflow-tools source tree.

## Validation

Run from this repository root with stable Rust and Git available:

```bash
cargo fmt --all -- --check
cargo clippy --all-targets --all-features --locked -- -D warnings
cargo test --locked
cargo build --locked --bin crane
cargo run --locked --bin crane -- transplant --help
```

Formatting uses stable rustfmt defaults, without a parent repository's
configuration. Tests create isolated temporary Git repositories and cover
filtered history, root rewrites, historical trees absent from the checkout,
dirty-target rejection, import-ref replacement, and dry-run metadata.
CI runs validation on Linux and Windows.

## Changes and review

Use a focused feature branch and submit a pull request to `main`. Preserve the
public mapping syntax and CLI behavior; add a regression test before fixing a
failure. For a history-transplant change, also review a real-source dry run
using the checklist in [README.md](README.md). A dry run alone is not live
import validation.

When updating a consumer submodule, keep its checkout attached to `main` or
an appropriate feature branch, integrate this repository first, and update
the consumer's gitlink afterward. Do not rewrite published history to update
the consumer.

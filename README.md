# crane-cli

CLI for transplanting filtered git history from selected source trees into another repository.

## Interface

Use `crane` when you need to preserve commit history for a narrow set of paths, remap those paths, and import the result into a target repository branch.

- `transplant`: resolve the first relevant commit for the selected path set, compute the narrowed history range, stream `git fast-export` through the Rust path transformer, and import the result with `git fast-import`.

## Usage

Build or run from this repository root:

```bash
cargo build --locked --bin crane
cargo run --locked --bin crane -- transplant --help
```

See [INSTALL.md](INSTALL.md) for prerequisites and installation, and
[CONTRIBUTING.md](CONTRIBUTING.md) for validation and contribution guidance.
The package remains `crane-cli`; the installed executable is `crane`.

## Branch-root rewrites

Use an empty destination mapping when the selected source tree should become the target branch root.

```bash
crane transplant \
  --source-repo . \
  --target-repo ../context-stack \
  --target-branch main \
  --import-branch crane/context-stack-root-review \
  --mapping crates/context-stack= \
  --dry-run
```

Use this mode when the selected subtree already represents the standalone repository root you want to review or import. Destination scopes must stay non-overlapping, so a branch-root mapping cannot be combined with other mappings that would land in the same root path space.

## Verification Flow

Before using `crane` for a production migration:

1. Run the focused crate tests.

```bash
cargo test --locked
```

2. Run a real dry run against the intended source and target repositories.

```bash
crane transplant \
  --source-repo . \
  --target-repo ../context-stack \
  --target-branch main \
  --import-branch crane/tools-review \
  --mapping tools/cli/context-cli=tools/cli/context-cli \
  --mapping tools/mcp/context-mcp=tools/mcp/context-mcp \
  --mapping tools/http/context-http=tools/http/context-http \
  --mapping tools/context-editor=tools/context-editor \
  --dry-run
```

Review the dry-run output before any live import. The required review metadata is:

- `source_ref`
- `source_commit`
- `anchor_commit`
- `range_spec`
- `target_branch`
- `import_branch`
- `import_ref`
- each emitted `mapping=<source>=<destination>` line

3. Only after the dry-run metadata looks correct should you run the live transplant and inspect the target repository history and cleanliness.

For an already relocated or deleted source tree, set `--source-ref` to the
last revision containing it. No source checkout change is needed. A dry run
reports planning metadata only; it does not execute the export/import or prove
that the target can merge cleanly. Use `--no-merge` for a live import into a
review branch before merging it yourself. Live imports require a clean target
and replace an existing import ref, so choose a dedicated branch.

## Repository history

This repository preserves its original placeholder history and seven filtered
development commits from `context-engine`, mapping `tools/cli/crane-cli` to the
repository root. The imported source tip was
[`d586648b3160d0587683a2fea8fee14b38354836`](https://github.com/mankinskin/context-engine/commit/d586648b3160d0587683a2fea8fee14b38354836),
before the October 17, 2026 source removal. All nine imported files were
verified against the original Git blobs before standalone changes.

The October relocation into `workflow-tools` was a byte-identical copy, not
the original development history. `workflow-tools` now consumes this
repository as a submodule; it no longer owns a second tracked copy.

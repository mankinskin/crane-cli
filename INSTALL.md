# Installation

## Prerequisites

- Git on `PATH`, with `fast-export` and `fast-import` support.
- Stable Rust and Cargo supporting edition 2024.
- The platform's Rust linker/build prerequisites.

There are no local path dependencies or sibling-repository requirements.
The committed Cargo lockfile defines the standalone dependency resolution.

## Install from source

```bash
git clone https://github.com/mankinskin/crane-cli.git
cd crane-cli
cargo install --path . --locked
crane --help
crane transplant --help
```

Cargo installs the `crane` executable into its configured binary directory,
normally `~/.cargo/bin`. Ensure that directory is on `PATH`.

To run without installation:

```bash
cargo run --locked --bin crane -- transplant --help
```

## Workflow-tools integration

Initialize the `crane-cli` submodule in a `workflow-tools` checkout, then use
the existing `crane` artifact selection:

```bash
git submodule update --init --merge crane-cli
git -C crane-cli switch main
install-ctl install crane --locked
```

Standalone installation does not require `workflow-tools` or `install-ctl`.

## Uninstall

```bash
cargo uninstall crane-cli
```

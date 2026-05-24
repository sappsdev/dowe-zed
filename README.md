# Dowe Zed

Dowe Zed is the dedicated Zed extension repository for Dowe Source Format files.

This repository contains the Zed extension adapter, language metadata, Tree-sitter queries, and the Dowe Tree-sitter grammar used by Zed. It recognizes `.dowe` files as `Dowe Source Format` and starts `dowe-language-server` over stdio.

The extension is maintained here directly. It is not generated from, embedded in, or installed through another Dowe repository.

## Requirements

- Zed with Rust extension development support.
- Rust installed through `rustup`.
- The `wasm32-wasip2` Rust target for local adapter builds.

The extension does not require Node.js, `node_modules`, npm, Prettier, or ESLint.

## Local Development

Install the Rust target used by Zed extensions if it is not already present:

```sh
rustup target add wasm32-wasip2
```

Prepare the local grammar mirror used by Zed dev extension installs:

```sh
./scripts/bootstrap-grammar-repo.sh
```

The bootstrap script creates `.zed-dev/tree-sitter-dowe.git` from the bundled `tree-sitter-dowe` directory and updates the grammar `rev` in `extension.toml`. Run it again after changing the grammar.

Build the extension adapter:

```sh
cargo check --target wasm32-wasip2
```

Install the extension in Zed with `zed: install dev extension` and select this repository directory.

Run local validation:

```sh
./scripts/check.sh
```

## Language Server

The published extension must not depend on a private Dowe checkout. By default, the adapter asks Zed to download `dowe-language-server` from public release assets on `dowe-lang/dowe-zed`.

Each release that should provide language-server features needs these assets:

```text
dowe-language-server-darwin-aarch64.tar.gz
dowe-language-server-darwin-x86_64.tar.gz
dowe-language-server-linux-aarch64.tar.gz
dowe-language-server-linux-x86_64.tar.gz
dowe-language-server-windows-aarch64.zip
dowe-language-server-windows-x86_64.zip
```

Each archive should contain the executable at its root:

- `dowe-language-server` for macOS and Linux.
- `dowe-language-server.exe` for Windows.

For local development only, a `dowe-language-server` binary on `PATH` is still accepted as a fallback or through Zed LSP binary settings.

## Publishing

Before publishing to the Zed extension registry, `extension.toml` must point at a public grammar source. Zed allows a Tree-sitter grammar to live in a subdirectory by using `path = "tree-sitter-dowe"`.

After committing grammar changes, prepare the manifest for publication:

```sh
./scripts/prepare-publish.sh
```

This changes the grammar entry to use `https://github.com/dowe-lang/dowe-zed`, the current `HEAD` commit, and `path = "tree-sitter-dowe"`.

Then open a PR to `zed-industries/extensions` that adds this repository as a submodule under `extensions/dowe` and adds the matching version to `extensions.toml`.

## Repository Layout

| Path | Purpose |
| --- | --- |
| `extension.toml` | Registers the Zed extension, grammar, and language server |
| `Cargo.toml` | Builds the WebAssembly extension adapter |
| `src/lib.rs` | Starts `dowe-language-server` for Zed |
| `languages/dowe/config.toml` | Registers `.dowe`, tab size, and grammar metadata |
| `languages/dowe/*.scm` | Tree-sitter queries for highlighting, indentation, outline, text objects, and brackets |
| `tree-sitter-dowe/grammar.js` | Tree-sitter grammar source |
| `tree-sitter-dowe/src/parser.c` | Generated Tree-sitter parser consumed by Zed |
| `scripts/bootstrap-grammar-repo.sh` | Builds the local git mirror used by Zed dev extension installs |
| `scripts/prepare-publish.sh` | Points the grammar at the public repository before publishing |
| `scripts/check.sh` | Runs local build and decoupling checks |

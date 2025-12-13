# Typst Out GitHub Action

This GitHub action compiles Typst files in your repository using a custom Typst version or ref, producing configurable output files and uploading them as artifacts.

> **Note**  
> This action is tested on **ubuntu**, **macos**, and **windows**.  
> **The Typst runs on CI may not have custom fonts available**, so please install any custom fonts you need **prior to compilation** to ensure your documents render correctly.

[![Test Typst Out Action](https://github.com/HackingGate/typst-out/actions/workflows/test_typst_out_action.yml/badge.svg)](https://github.com/HackingGate/typst-out/actions/workflows/test_typst_out_action.yml)

## Features

- **Configurable Typst version or ref** for compiling your Typst files (default: `latest`).
- Option to **set the number of days to retain** the compiled outputs as artifacts.
- **Customizable naming convention** for the artifacts.
- **Configurable output file extensions** (e.g., `pdf`).
- Ability to **use a custom template file**.
- Specify a **custom font path** if you want to install or reference fonts in your builds.

## Usage

To use the Typst Out action in your GitHub repository, create or update a workflow file (e.g., `.github/workflows/typst_out.yml`) with content similar to:

```yaml
on:
  push:
    paths:
      - "**.typ"

jobs:
  build_typst:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Compile Typst files
        uses: HackingGate/typst-out@v3.1.0
        with:
          typst_version: latest
          retention_days: 7
          artifacts_name: typst_output
          template_file: template.typ
```

## Inputs

| Name             | Description                                                         | Required | Default        |
| ---------------- | ------------------------------------------------------------------- | -------- | -------------- |
| `typst_version`  | The version or ref of Typst to use for compilation                  | No       | `latest`       |
| `retention_days` | The number of days to retain the compiled PDFs (or other artifacts) | No       | `7`            |
| `artifacts_name` | The name of the artifacts to upload                                 | No       | `typst_output` |
| `template_file`  | The template file to use (if applicable)                            | No       | `template.typ` |

## Caching

This GitHub action employs caching to speed up workflows and reduce build times:

1. **Cache Typst binary**:  
   Caches the installed Typst binary at `~/.cargo/bin/typst` using `actions/cache@v4`. The cache key depends on the Typst version/commit (determined by the input `typst_version`). When a cache hit occurs, the installation step is skipped entirely.

2. **Cache Rust dependencies**:  
   Uses `Swatinem/rust-cache@v2` to cache Rust compilation artifacts, speeding up Typst installation from source when needed.

The action uses `cargo install --git` to install Typst directly from the official repository, ensuring you always get the exact version specified.

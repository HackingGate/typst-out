# Typst Out GitHub Action

This GitHub action builds Typst files in your repository using a custom Typst ref, producing configurable output files and uploading them as artifacts.

> **Note**  
> This action is tested on **ubuntu**, **macos**, and **windows**.  
> **The Typst runs on CI may not have custom fonts available**, so please install any custom fonts you need **prior to the build** to ensure your documents render correctly.

[![Test Typst Out Action](https://github.com/HackingGate/typst-out/actions/workflows/test_typst_out_action.yml/badge.svg)](https://github.com/HackingGate/typst-out/actions/workflows/test_typst_out_action.yml)

## Features

- **Configurable Typst version or ref** for building your Typst files (default: `latest`).
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

      - name: Build Typst files
        uses: HackingGate/typst-out@v1
        with:
          typst_version: latest
          retention_days: 7
          artifacts_name: typst_output
          template_file: template.typ
```

## Inputs

| Name                | Description                                                               | Required | Default        |
|---------------------|---------------------------------------------------------------------------|----------|----------------|
| `typst_version`     | The version or ref of Typst to use for building                           | No       | `latest`       |
| `retention_days`    | The number of days to retain the compiled PDFs (or other artifacts)       | No       | `7`            |
| `artifacts_name`    | The name of the artifacts to upload                                       | No       | `typst_output` |
| `template_file`     | The template file to use (if applicable)                                  | No       | `template.typ` |

## Caching

This GitHub action employs caching to speedily store and recover build artifacts and dependencies, thereby reducing build times:

1. **Cache Typst build**:  
   Caches the built Typst binary using `actions/cache@v4`. The cache key depends on the Typst commit hash (determined by the input `typst_version`). If a cache hit is found, the Rust setup and Typst build steps are skipped, saving time.

2. **Cache Rust**:  
   If there is a cache miss for the Typst build, the action uses `Swatinem/rust-cache@v2` to cache Rust dependencies. This reduces setup time for Rust and subsequent Typst compilation steps.

With the caching mechanism in place, the action runs faster on subsequent workflows that use the same Typst commit or version references.
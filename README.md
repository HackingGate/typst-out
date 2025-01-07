# Typst Out GitHub Action

This GitHub action builds Typst files in your repository using a custom Typst ref, producing configurable output files and uploading them as artifacts.

[![Test Typst Out Action](https://github.com/HackingGate/typst-out/actions/workflows/test_typst_out_action.yml/badge.svg)](https://github.com/HackingGate/typst-out/actions/workflows/test_typst_out_action.yml)

## Features

- Configurable Typst ref for building your Typst files.
- Option to set the number of days to retain the compiled outputs as artifacts.
- Customizable naming convention for the artifacts.
- Configurable output file extensions.
- Ability to use a custom template file.
- Specify your own font path.

## Usage

To leverage the Typst Out action in your GitHub repository, create a new workflow file (e.g., `.github/workflows/typst_out.yml`) and add the content shown below:

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
          typst_ref: main
          retention_days: 7
          artifacts_name: typst_output
          output_extensions: pdf
          template_file: template.typ
```

To use the latest release of Typst, you can utilize the `pozetroninc/github-action-get-latest-release` action to fetch the latest release from `typst/typst`.

```yaml
steps:
  - name: Checkout repository
    uses: actions/checkout@v4

  - name: Get latest release of Typst
    id: get-latest-release
    uses: pozetroninc/github-action-get-latest-release@master
    with:
      repository: typst/typst

  - name: Build Typst files
    uses: HackingGate/typst-out@main
    with:
      typst_ref: ${{ steps.get-latest-release.outputs.release }}
```

## Inputs

| Name                | Description                                                  | Required | Default        |
| ------------------- | ------------------------------------------------------------ | -------- | -------------- |
| `typst_ref`         | The ref of Typst for building                                | No       | `main`         |
| `retention_days`    | Number of days to keep the PDFs as artifacts                 | No       | `7`            |
| `artifacts_name`    | Name for the uploaded artifacts                              | No       | `typst_output` |
| `output_extensions` | Output file extensions                                       | No       | `pdf`          |
| `template_file`     | Template file to utilize                                     | No       | `template.typ` |
| `fonts_path`        | Specify the path for custom fonts (if not using Typst fonts) | No       | `''`           |

## Caching

This GitHub action employs caching to speedily store and recover build artifacts and dependencies. This optimizes the action's execution time by preventing the need to rebuild the Typst binary and reacquire Rust dependencies on every run.

There are three main caching phases in this action:

1.**Cache Typst build**: This phase caches the constructed Typst binary using the `actions/cache@v3` action. The cache key is determined by the Typst commit SHA from the given `typst_ref`. If a cache hit is detected (i.e., a stored Typst binary is found for the commit SHA), the subsequent Rust setup and Typst building steps are skipped, directly proceeding to Typst file compilation.

2.**Cache Typst fonts**: This caches fonts from `typst/assets/fonts` with the `actions/cache@v3` action. The cache key is again derived from the Typst commit SHA. If there's a cache hit, the font acquisition step is omitted.

3.**Cache Rust**: When a cache miss occurs for the Typst build, Rust dependencies are cached using the `Swatinem/rust-cache@v2` action. This substantially diminishes setup time for Rust and the Typst binary compilation in subsequent runs.

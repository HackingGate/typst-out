# Typst Out GitHub Action
This GitHub action automatically builds Typst files in your repository using a custom Typst ref and uploads the output files as artifacts.

[![Test](https://github.com/HackingGate/typst-out/actions/workflows/test.yml/badge.svg)](https://github.com/HackingGate/typst-out/actions/workflows/test.yml)

## Features

- Uses a custom Typst ref to build your Typst files
- Allows for setting the number of days to retain the PDFs as artifacts
- Supports custom naming of the artifacts
- Configurable output file extensions
- Allows for using a custom template file

## Usage

To use the Typst Out action in your GitHub repository, create a new workflow file (e.g., `.github/workflows/typst_out.yml`) and add the following content:

```yaml
on:
  push:
    paths:
      - '**.typ'

jobs:
  build_typst:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Build Typst files
        uses: HackingGate/typst-out@v1
        with:
          typst_ref: main
          retention_days: 7
          artifacts_name: typst_output
          output_extensions: pdf
          template_file: template.typ
```

To use the latest release of Typst, you can use the `pozetroninc/github-action-get-latest-release` action to get the latest release from `typst/typst`.

```yaml
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

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

| Name | Description | Required | Default |
| --- | --- | --- | --- |
| `typst_ref` | The ref of Typst to use for building | No | `main` |
| `retention_days` | The number of days to retain the PDFs as artifacts | No | `7` |
| `artifacts_name` | The name of the artifacts to upload | No | `typst_output` |
| `output_extensions` | The extensions of the output files | No | `pdf` |
| `template_file` | The template file to use | No | `template.typ` |

## Caching

In this GitHub action, caching is used to store and retrieve the build artifacts and dependencies to speed up the action's runtime. Caching helps avoid rebuilding the Typst binary and reinstalling the Rust dependencies each time the action is run, saving time and resources.

There are three caching steps in this action:

1. **Cache Typst build**: This step caches the built Typst binary using the `actions/cache@v3` action. The cache key is created using the Typst commit SHA from the specified `typst_ref`. When the action is run, it checks if there's a cache hit (i.e., if a cached Typst binary exists for the given commit SHA). If there's a cache hit, the action skips the subsequent steps for setting up Rust and building Typst, and directly proceeds to the step for compiling Typst files.

```yaml
- name: Cache Typst build
  uses: actions/cache@v3
  id: typst_cache
  with:
    path: ~/typst_build
    key: typst-${{ steps.typst_commit_sha.outputs.sha }}
```

2. **Cache Typst font**: This step caches the fonts in the `typst/assets/fonts` directory using the `actions/cache@v3` action. The cache key is created using the Typst commit SHA from the specified `typst_ref`. When the action is run, it checks if there's a cache hit (i.e., if a cached set of fonts exists for the given commit SHA). If there's a cache hit, the action skips the subsequent step for fetching the fonts and proceeds to the next steps in the workflow. Caching the fonts helps reduce the time taken to install fonts in future runs.

```yaml
- name: Cache Typst fonts
  uses: actions/cache@v3
  with:
    path: ~/typst_fonts
    key: fonts-${{ steps.typst_commit_sha.outputs.sha }}
```

3. **Cache Rust**: If there's no cache hit for the Typst build, this step caches the Rust dependencies using the `Swatinem/rust-cache@v2` action. By caching the Rust dependencies, the action can significantly reduce the time taken to set up Rust and compile the Typst binary in future runs.

```yaml
- name: Cache Rust
  if: steps.typst_cache.outputs.cache-hit != 'true'
  uses: Swatinem/rust-cache@v2
```

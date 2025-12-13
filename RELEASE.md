# Release Process

This document describes the process for creating a new release of the Typst Out GitHub Action.

## Prerequisites

- Ensure all changes are merged to the `main` branch
- Ensure all tests are passing on the `main` branch
- Determine the appropriate version number following [Semantic Versioning](https://semver.org/):
  - **MAJOR** version (X.0.0): incompatible API changes
  - **MINOR** version (0.X.0): new functionality in a backward compatible manner
  - **PATCH** version (0.0.X): backward compatible bug fixes

## Steps

### 1. Update CHANGELOG.md

Add a new section at the top of `CHANGELOG.md` with:
- The new version number and date
- All changes since the last release, organized by type:
  - **Added**: new features
  - **Changed**: changes in existing functionality
  - **Deprecated**: soon-to-be removed features
  - **Removed**: removed features
  - **Fixed**: bug fixes
  - **Security**: security improvements

### 2. Update README.md

Update the version number in usage examples from the old version to the new version:

```yaml
uses: HackingGate/typst-out@vX.Y.Z
```

### 3. Commit Changes

```bash
git add CHANGELOG.md README.md
git commit -m "Prepare release vX.Y.Z"
git push origin main
```

### 4. Create and Push Tag

```bash
git tag vX.Y.Z
git push origin vX.Y.Z
```

### 5. Automated Release

The GitHub Actions workflow (`.github/workflows/release.yml`) will automatically:
- Extract the changelog for the version
- Create a GitHub release with the tag
- Attach release notes from the CHANGELOG

### 6. Verify Release

1. Go to https://github.com/HackingGate/typst-out/releases
2. Verify the release was created with the correct version and notes
3. Test the action with the new version tag

## Example

For release v3.1.0:

```bash
# Update CHANGELOG.md and README.md
git add CHANGELOG.md README.md
git commit -m "Prepare release v3.1.0"
git push origin main

# Create and push tag
git tag v3.1.0
git push origin v3.1.0
```

The GitHub Actions workflow will handle the rest automatically.

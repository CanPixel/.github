# CanPixel `.github`

Shared GitHub configuration and reusable CI workflows for the CanPixel organization.

This repository is the central place for:
- Reusable GitHub Actions workflows
- Organization-wide CI conventions
- Shared quality/security automation documentation

---

## Structure

```text
.github/
  workflows/
    typescript-checks.yml   # reusable workflow
README.md
```

> Reusable workflows **must** live in `.github/workflows/` to be callable from other repositories.

---

## Using a reusable workflow from another repo

In a consuming repository, create e.g. `.github/workflows/quality.yml`:

```yaml
name: Quality

on:
  pull_request:
  workflow_dispatch:

jobs:
  ts-checks:
    uses: CanPixel/.github/.github/workflows/typescript-checks.yml@main
    with:
      package-manager: auto
      node-version: "20"
      run-lint: true
      run-tsc: true
      run-prettier: true
```

If the reusable workflow requires secrets, add:

```yaml
secrets: inherit
```

---

## Versioning policy

Prefer pinning to a tag or commit SHA for stability in production repos:

```yaml
uses: CanPixel/.github/.github/workflows/typescript-checks.yml@v1
# or
uses: CanPixel/.github/.github/workflows/typescript-checks.yml@<commit-sha>
```

Use `@main` only during early iteration.

---

## Design principles

1. Keep workflows small and composable.
2. Expose behavior via `workflow_call.inputs` (avoid hardcoded repo assumptions).
3. Fail fast and explicitly.
4. Optimize CI minutes with caching and change filters.
5. Maintain local/CI parity where practical.

---

## Local parity expectation

Reusable workflows run in CI.  
For local parity, each app repo should provide scripts (for example):

- `lint`
- `tsc --noEmit`
- `prettier --check .`

---

## Troubleshooting

### `Unable to find reusable workflow`
- Confirm file exists at:
  `.github/workflows/<name>.yml`
- Confirm `uses:` includes **owner/repo/path@ref**:
  `CanPixel/.github/.github/workflows/typescript-checks.yml@main`
- Confirm the referenced branch/tag contains the file.

### `Invalid workflow reference`
- Usually caused by missing repo segment in `uses:`
- Must be `owner/repository/...`, not `owner/...`

---

## Contribution flow

1. Open PR in `CanPixel/.github`
2. Validate on one pilot repository first
3. Roll out to more repositories after successful pilot run

---

## Scope note

This repo should contain **shared automation**, not product/application code.

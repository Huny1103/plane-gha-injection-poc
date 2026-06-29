# Plane GitHub Actions Injection PoC

This repository is a controlled reproduction for a GitHub Actions shell injection pattern found in Plane's feature deployment workflow.

The important file is:

```text
.github/workflows/poc.yml
```

`poc.yml` is a GitHub Actions workflow file. When this directory is pushed to a GitHub repository, GitHub reads files under `.github/workflows/` and shows them in the `Actions` tab.

## How To Run

1. Push this folder to a GitHub repository.
2. Open the repository on GitHub.
3. Go to `Actions`.
4. Select `Plane Feature Deployment Injection PoC`.
5. Click `Run workflow`.
6. In `base_tag_name`, paste:

```text
$(echo INJECTED_FROM_WORKFLOW_INPUT >/tmp/plane_gha_injected.txt)preview
```

7. Open the workflow logs.

If the reproduction works, the log will show:

```text
INJECTION_CONFIRMED
INJECTED_FROM_WORKFLOW_INPUT
```

## What This Proves

The workflow places untrusted `workflow_dispatch` input directly into a Bash `run:` block:

```yaml
if [ "${{ github.event.inputs.base_tag_name }}" != "" ]; then
```

GitHub Actions expands `${{ github.event.inputs.base_tag_name }}` before Bash executes the script. A payload containing Bash syntax can therefore execute commands in the runner.

This PoC only writes a harmless file under `/tmp` in the temporary GitHub Actions runner.

---
description: Apply code changes to python-prism based on instructions from a go-prism PR description
triggers:
  - pull_request:
      types: [opened, reopened, synchronize]
model: copilot
tools:
  - bash
  - edit
  - create
  - view
  - grep
  - glob
permissions:
  contents: write
  pull-requests: write
network:
  allowed:
    - python
    - pip
---

# Apply Changes from go-prism PR

You are an AI coding agent working on the **python-prism** repository — a Python port of the go-prism project.

When a PR is opened or updated (typically triggered from a go-prism workflow), your task is to:

1. **Read the PR description** — it contains a detailed summary of changes that need to be applied to this Python repository.
2. **Understand the intent** — the changes described are from the Go implementation and must be adapted idiomatically to Python (using Python conventions, type hints, standard library, etc.).
3. **Apply the changes** — modify, create, or delete files in `src/python_prism/` and `tests/` as instructed.
4. **Validate the changes** — run lint, type-check, and tests to ensure the changes are correct.
5. **Commit and push** — commit all changes with a descriptive message and push to this PR branch.
6. **Post a comment** — summarize what was done and report the outcome of lint/tests as a PR comment.

## Step-by-step Instructions

### 1. Read the PR

Read the body of the current pull request. It will contain the change summary, e.g.:

> "Add a new `PrismConfig` struct/class that holds configuration for the prism pipeline. Add a `load_config(path: str) -> PrismConfig` function that reads a YAML file and returns the config object. Add unit tests for both."

### 2. Explore the repository

Use `view`, `glob`, and `grep` to understand the current structure of `src/python_prism/` and `tests/` before making any changes.

### 3. Implement the changes

- Work only within `src/python_prism/` and `tests/`.
- Follow the existing code style (ruff line length 100, type hints throughout, `pytest`-based tests).
- If a new dependency is needed, add it to `pyproject.toml` under `[project].dependencies`.
- Do **not** modify `.github/` files.

### 4. Lint, type-check, and test

Run the following commands and fix any issues before committing:

```bash
pip install -e ".[dev]"
ruff check src tests
mypy src
pytest
```

### 5. Commit and push

Once all checks pass:

```bash
git config user.email "github-actions[bot]@users.noreply.github.com"
git config user.name "github-actions[bot]"
git add .
git commit -m "apply(<scope>): <one-line summary derived from the PR description title or first sentence>"
git push
```

Where `<scope>` is the primary module affected (e.g., `config`, `pipeline`, `utils`) and the summary is drawn directly from the PR description.

### 6. Post a summary comment

Post a comment on the PR that includes:
- A bullet list of files created/modified/deleted
- The result of `ruff`, `mypy`, and `pytest` (pass/fail)
- Any caveats or manual follow-up steps required

## Usage

This workflow is triggered automatically when a PR is opened or updated in this repository. The PR description **must** contain the change instructions for the agent to act on. If the description is empty or does not contain actionable instructions, post a comment explaining that no changes were applied and ask the PR author to provide a description.

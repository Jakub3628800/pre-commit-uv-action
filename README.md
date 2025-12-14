# pre-commit-uv-action

Run [pre-commit](https://pre-commit.com/) hooks with [uv](https://github.com/astral-sh/uv) for fast Python environment management.

**Features**: Fast dependency installation, automatic caching, no setup-python needed, three operation modes.

## Dependencies

This action uses:
- [astral-sh/setup-uv@v7](https://github.com/astral-sh/setup-uv) - Install uv
- [actions/cache@v4](https://github.com/actions/cache) - Cache pre-commit environments
- [peter-evans/create-pull-request@v7](https://github.com/peter-evans/create-pull-request) - Create PRs for autoupdate

## Usage

### Basic (isolated)

```yaml
- uses: actions/checkout@v4
- uses: jk/pre-commit-uv-action@v1
  with:
    extra_args: --all-files
```

### With project dependencies

For local hooks that need your project's code:

```yaml
- uses: jk/pre-commit-uv-action@v1
  with:
    command: run-venv
    extra_args: --all-files
```

### Auto-update with PR

```yaml
- uses: jk/pre-commit-uv-action@v1
  with:
    command: autoupdate
    pr-assignees: username
    pr-reviewers: username
    pr-base: master
```

## Inputs

### General inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `command` | Command to run: `run`, `run-venv`, or `autoupdate` | No | `run` |
| `extra_args` | Arguments passed to `pre-commit run` (e.g., `--all-files`, hook-id) | No | `''` |

### Autoupdate PR inputs

These inputs only apply when `command: autoupdate`:

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `pr-title` | PR title | No | `chore: Pre-commit auto-update` |
| `pr-body` | PR body | No | `Automated update of pre-commit hooks` |
| `pr-branch` | PR branch name | No | `pre-commit-autoupdate` |
| `pr-base` | PR base branch | No | `main` |
| `pr-labels` | Comma-separated PR labels | No | `dependencies,github_actions` |
| `pr-assignees` | Comma-separated PR assignees | No | `''` |
| `pr-reviewers` | Comma-separated PR reviewers | No | `''` |
| `pr-sign-commits` | Sign commits | No | `true` |

## Commands

- **`run`** (default): Isolated environment via `uv tool run pre-commit`
- **`run-venv`**: With project deps via `uv sync` then `uv run pre-commit`
- **`autoupdate`**: Update hooks and create PR

## License

MIT

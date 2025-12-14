# pre-commit-uv-action

Run [pre-commit](https://pre-commit.com/) hooks with [uv](https://github.com/astral-sh/uv) for fast Python environment management.

A drop-in replacement for [pre-commit/action](https://github.com/pre-commit/action) that uses `uv` instead of `pip`.

## Usage

### Basic pre-commit check

```yaml
name: pre-commit
on: [push, pull_request]

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v7
        with:
          enable-cache: true
      - uses: Jakub3628800/pre-commit-uv-action@v0.0.1
```

This runs all hooks on all files by default.

### Run specific hook or files

```yaml
- uses: Jakub3628800/pre-commit-uv-action@v0.0.1
  with:
    extra_args: ruff  # Run only ruff hook
```

```yaml
- uses: Jakub3628800/pre-commit-uv-action@v0.0.1
  with:
    extra_args: --files src/**/*.py  # Run on specific files
```

### With project dependencies (for local hooks)

If you have local hooks that need to import your project code:

```yaml
- uses: actions/checkout@v4
- uses: astral-sh/setup-uv@v7
  with:
    enable-cache: true
- uses: Jakub3628800/pre-commit-uv-action@v0.0.1
  with:
    command: run-venv
```

This runs `uv sync` first to install your project dependencies.

### Auto-update hooks (scheduled)

Create a separate workflow to automatically update hook versions:

```yaml
name: pre-commit autoupdate
on:
  schedule:
    - cron: '0 0 * * 1'  # Weekly on Monday
  workflow_dispatch:

jobs:
  autoupdate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: astral-sh/setup-uv@v7
        with:
          enable-cache: true

      - uses: Jakub3628800/pre-commit-uv-action@v0.0.1
        with:
          command: autoupdate

      - uses: peter-evans/create-pull-request@v7
        with:
          title: "chore: Update pre-commit hooks"
          body: "Automated update of pre-commit hook versions"
          branch: pre-commit-autoupdate
          commit-message: "chore: Update pre-commit hooks"
          labels: dependencies
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `command` | Command: `run`, `run-venv`, or `autoupdate` | No | `run` |
| `extra_args` | Arguments to pass to pre-commit | No | `--all-files` |

## Commands

- **`run`**: Run pre-commit in isolated mode using `uv tool run pre-commit`
- **`run-venv`**: Install project deps with `uv sync` then run with `uv run pre-commit`
- **`autoupdate`**: Update hook versions in `.pre-commit-config.yaml`

## Why use this action?

- **Fast**: Uses `uv` instead of `pip` for significantly faster tool installation
- **Cached**: Automatically caches pre-commit hook environments (`~/.cache/pre-commit`)
- **No setup-python needed**: `uv` manages Python installations
- **Simple**: Drop-in replacement for the official action with minimal configuration

## Comparison with official action

**Official pre-commit/action:**
```yaml
- uses: actions/setup-python@v5
- uses: pre-commit/action@v3.0.1
```

**This action:**
```yaml
- uses: astral-sh/setup-uv@v7
- uses: Jakub3628800/pre-commit-uv-action@v0.0.1
```

## Dependencies

- [astral-sh/setup-uv@v7](https://github.com/astral-sh/setup-uv) (required, install externally)
- [actions/cache@v4](https://github.com/actions/cache) (used internally for caching)
- [peter-evans/create-pull-request@v7](https://github.com/peter-evans/create-pull-request) (optional, for autoupdate)

## License

MIT

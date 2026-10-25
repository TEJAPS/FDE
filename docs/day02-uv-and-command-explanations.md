# Day 2 — Understanding uv, Project Isolation and Every Setup Command

**Status:** Environment verified; learning notes completed before first FastAPI endpoint. **Local directory:** `E:\FDE`. **Verified by learner:** Python 3.13.5, FastAPI 0.162.0, pytest 9.1.1, Ruff 0.17.2 (via `uv run`). Global tool inventory: Git 2.51.0.windows.1, uv 0.8.22, Docker CLI 28.4.0, VS Code 1.117.0, global `python` 3.13.5, `py` 3.14.2.

## Purpose
Understand *why* the FDE project uses uv, what the generated files mean, and how each command contributes to a reproducible Windows/macOS Python environment. Distinguish successful environment verification from implemented/tested application behavior.

## What was accomplished
1. Cloned `TEJAPS/FDE` into `E:\FDE` and opened it in VS Code.
2. Pinned the project Python family to 3.13.
3. Initialized minimal `pyproject.toml` metadata.
4. Created/synchronized the isolated `.venv`.
5. Added FastAPI and Uvicorn as application dependencies.
6. Added pytest, HTTPX and Ruff as development dependencies.
7. Verified Python, FastAPI, pytest and Ruff inside the uv project environment.

The user-provided successful verification output establishes these results. Docker engine connectivity, a running API, and passing application tests have **not** been demonstrated yet.

## Why uv?
uv combines Python version selection, environment management, dependency resolution, lockfile generation, package installation and project-aware command execution. It helps avoid global dependency conflicts and makes onboarding consistent.

**Java analogy (approximate):** Python 3.13 is like the JDK; `pyproject.toml` resembles a Maven POM; `uv.lock` records exact resolved dependencies; `uv sync` restores the environment; `uv run pytest` resembles a project-scoped test command. Python's `.venv` provides explicit interpreter/package isolation, unlike a typical Java classpath.

## Files and their responsibilities
| File/directory | Purpose | Commit? |
|---|---|---|
| `.git/` | Local Git metadata | Managed by Git; never commit manually |
| `.python-version` | Preferred Python version family (3.13) | Yes |
| `pyproject.toml` | Project metadata, Python requirement, direct dependencies and dependency groups | Yes |
| `uv.lock` | Exact resolved package graph/versions for repeatability | Yes |
| `.venv/` | Machine-local interpreter and installed dependencies | No |
| `docs/` | FDE learning notes | Yes |

`pyproject.toml` is similar in purpose to `pom.xml`. `uv.lock` records the resolved dependency graph, including transitive dependencies. Committing the lockfile helps collaborators reproduce the environment.

## Setup commands explained
| Command | Why it is used | Notes |
|---|---|---|
| `Test-Path E:\` | Check that the selected Windows drive exists | PowerShell only |
| `Set-Location E:\` | Change to the workspace drive | PowerShell only |
| `git clone https://github.com/TEJAPS/FDE.git` | Copy repository files and history locally | Only if not already cloned |
| `Set-Location E:\FDE` | Enter the project folder | Run subsequent project commands here |
| `code .` | Open the current folder in VS Code | `code` must be on PATH |
| `git status` | Inspect branch, modified and untracked files | Check before staging/committing |
| `git pull --ff-only` | Update existing checkout without creating a merge commit | Use only with clean/understood local changes |
| `uv python pin 3.13` | Write `.python-version` | Selects Python 3.13 family |
| `uv init --bare --python 3.13` | Create minimal project metadata | Run once, only if `pyproject.toml` absent |
| `uv sync` | Synchronize project `.venv` with declared/locked dependencies | Standard post-clone command |
| `uv add fastapi "uvicorn[standard]"` | Add runtime API framework and ASGI server | Updates project metadata/lock/environment |
| `uv add --dev pytest httpx ruff` | Add testing, HTTP test-client and lint/format tools | Dev group, not app runtime requirements |
| `uv run python --version` | Check project-selected Python | Verified 3.13.5 |
| `uv run python -c "import fastapi; print(fastapi.__version__)"` | Check FastAPI import/version | Verified 0.162.0; `-c` executes inline Python |
| `uv run pytest --version` | Check test runner installed | Verified 9.1.1; does not run tests |
| `uv run ruff --version` | Check linter installed | Verified 0.17.2; does not lint code |
| `docker --version` | Check Docker CLI installed | Does not prove daemon running |
| `docker info` | Check Docker engine connectivity | Still needs verification |

## `uv run` vs bare `python`
The Windows shell prompt displays `(base)`, indicating an active Conda base environment. Bare `python` can resolve to a globally selected or Conda interpreter. `uv run python` runs with the project's uv-managed environment, making interpreter and package selection reliable without manually activating `.venv`.

## Dependencies explained
- **FastAPI:** HTTP routing, typed request/response validation and API documentation.
- **Uvicorn:** ASGI server that serves the FastAPI application; `[standard]` enables optional performance/convenience packages.
- **Pydantic:** Data validation used by FastAPI.
- **pytest:** Automated test runner.
- **HTTPX:** HTTP client and useful testing companion.
- **Ruff:** Fast Python linter and formatter.

## Reproducibility and collaboration
On another Windows/macOS machine, clone the repository, install uv, and run `uv sync`. Commit `.python-version`, `pyproject.toml` and `uv.lock`; never commit `.venv`, caches, `.env` or credentials. When versions change intentionally, use `uv add` and review the resulting lockfile diff.

## Checkpoint
**Environment verification passed.** Next: create a minimal `app/main.py` exposing `GET /health`, start it using `uv run uvicorn app.main:app --reload`, add tests, run `uv run pytest -q` and `uv run ruff check .`. No endpoint has been implemented or tested yet.

Related: [Installation guide](day02-development-stack-and-installation.md) · [Command reference](day02-command-reference.md).

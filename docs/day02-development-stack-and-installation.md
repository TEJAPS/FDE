# Day 2 — Development Stack and Installation (Windows + macOS)

**Status:** Onboarding reference. **Local project path (Windows):** `E:\FDE`. **macOS example:** `~/dev/FDE`.

## Purpose
Provide a reproducible local development environment for the FDE incident-assistance capstone. Explain the stack, prerequisites, installation commands, environment verification, project setup and troubleshooting for both Windows and macOS. Actual application implementation and deployment are not yet complete.

## Verified Windows inventory (reported 2026-10-25)
| Component | User-reported command | Version / observation | Project decision |
|---|---|---|---|
| Python on PATH | `python --version` | 3.13.5 | Pin project to Python 3.13 |
| Windows Python launcher | `py --version` | 3.14.2 | Launcher reports 3.14.2; use `py -0p` to list installations |
| Git | `git --version` | 2.51.0.windows.1 | Use existing installation |
| uv | `uv --version` | 0.8.22 | Use existing installation |
| Docker CLI | `docker --version` | 28.4.0, build d8eb465 | CLI installed; daemon not yet verified |
| VS Code | `code --version` | 1.117.0, x64 | Use existing installation |
| Shell | PowerShell prompt | `(base)` | Conda base active; uv-managed project .venv remains isolated |

**Important:** These versions are an observed snapshot of one Windows laptop, **not required minimums for macOS**. Do not assume Docker Desktop is running just because the CLI prints a version. Dependencies are intentionally unpinned until the first `uv add` produces a lockfile.

## Stack and purpose
| Tool | Purpose | Required for Day 2? |
|---|---|---|
| Git | Clone, commit, push | Yes |
| Python 3.13 | API runtime | Yes |
| uv | Python versions, .venv, dependency resolution, lockfile | Yes |
| VS Code + Python extension | Editing, terminal, interpreter/debugger | Recommended |
| FastAPI | Typed REST API | Yes |
| Uvicorn | Local ASGI server | Yes |
| Pydantic | Request/response validation (FastAPI dependency) | Indirectly |
| pytest + HTTPX | Automated API tests | Yes |
| Ruff | Formatting and linting | Yes |
| Docker Desktop / Docker Engine | Containers and future integration testing | Install/verify; not needed for first endpoint |
| PostgreSQL + pgvector | Future retrieval storage | Later, not Day 2 |
| LLM API credentials, LangGraph, Camunda | Future features | Not Day 2 |
| GitHub Copilot | Optional AI coding assistant | Not required; no subscription needed |

## Windows installation (fresh machine)
Use **PowerShell**. `winget` comes with App Installer on supported Windows systems; if unavailable, use official installers.

### 1. Check package manager
```powershell
winget --version
```

### 2. Install core tools (skip already-installed components)
```powershell
winget install --id Python.Python.3.13 -e
winget install --id Git.Git -e
winget install --id Microsoft.VisualStudioCode -e
winget install --id astral-sh.uv -e
winget install --id Docker.DockerDesktop -e
```

If a package ID is unavailable, use `winget search <name>` and the official vendor installer. Docker Desktop may require WSL 2, virtualization enabled, a supported Windows edition/configuration and a restart. Launch Docker Desktop separately.

### 3. Verify versions and Python installations
```powershell
python --version
py --version
py -0p
git --version
uv --version
docker --version
code --version
docker info
```
`docker info` verifies daemon connectivity; `docker --version` only checks the CLI.

### 4. Optional: VS Code extensions
```powershell
code --install-extension ms-python.python
code --install-extension charliermarsh.ruff
```

## macOS installation (fresh machine)
Use **Terminal** with Homebrew. Install Homebrew from its official instructions at https://brew.sh/ and follow the printed shell setup commands. The following assumes `brew` is on PATH.

### 1. Install core tools
```bash
brew install python@3.13 git uv
brew install --cask visual-studio-code docker-desktop
```

Homebrew package/cask names can change: confirm with `brew info <formula>` or `brew search <name>` if a command fails. Launch Docker Desktop and complete its setup. VS Code's `code` shell command may need to be installed from the Command Palette: **Shell Command: Install 'code' command in PATH**.

### 2. Verify
```bash
python3.13 --version
git --version
uv --version
docker --version
code --version
docker info
```

### 3. Optional VS Code extensions
```bash
code --install-extension ms-python.python
code --install-extension charliermarsh.ruff
```

## Clone and initialize the existing FDE repository

### Windows — preferred path `E:\FDE`
```powershell
Set-Location E:\
git clone https://github.com/TEJAPS/FDE.git
Set-Location E:\FDE
code .
```
If already cloned, **do not clone again**:
```powershell
Set-Location E:\FDE
git status
git pull --ff-only
code .
```

### macOS — example path `~/dev/FDE`
```bash
mkdir -p ~/dev
cd ~/dev
git clone https://github.com/TEJAPS/FDE.git
cd ~/dev/FDE
code .
```
If already cloned:
```bash
cd ~/dev/FDE
git status
git pull --ff-only
code .
```

## Initialize Python project (same commands on both OSes)
**Run inside the cloned FDE repository.** `uv init --bare` should be used only if `pyproject.toml` does not exist; if the project is already initialized, skip that command.

```text
uv python pin 3.13
uv init --bare --python 3.13
uv sync
uv add fastapi "uvicorn[standard]"
uv add --dev pytest httpx ruff
```

`uv python pin 3.13` writes `.python-version`; `uv init --bare` writes `pyproject.toml`; `uv sync` creates the local `.venv`; `uv add` updates dependencies and `uv.lock`. If uv cannot locate Python 3.13, run `uv python install 3.13` first. Avoid mixing `pip install` or Conda installs into this uv-managed environment.

**Recommended Git tracking:** commit `pyproject.toml`, `uv.lock`, `.python-version`, code and tests. Ignore `.venv/`, `__pycache__/`, `.pytest_cache/`, `.ruff_cache/`, `.env` and secrets.

### Verify project environment
```text
uv run python --version
uv run python -c "import fastapi; print(fastapi.__version__)"
uv run pytest --version
uv run ruff --version
```

### VS Code interpreter
Choose **Python: Select Interpreter** and select `E:\FDE\.venv\Scripts\python.exe` on Windows, or `~/dev/FDE/.venv/bin/python` on macOS. The `uv run` commands work without manually activating the virtual environment.

## Troubleshooting
- **`(base)` Conda prefix:** indicates Conda base is active; use `uv run` to select the project interpreter. Optionally run `conda deactivate` if you want a cleaner shell.
- **`python` and `py` show different versions:** multiple Python installations are normal. `uv run python --version` is the relevant project check.
- **`uv init` says already initialized:** skip it and run `uv sync`.
- **`git clone` destination exists:** use `cd`/ `Set-Location` and `git pull --ff-only` after reviewing local changes.
- **Docker daemon unavailable:** start Docker Desktop, confirm WSL 2 / virtualization on Windows, then retry `docker info`.
- **`code` not found on macOS:** install shell command in PATH from VS Code Command Palette.
- **PowerShell activation policy:** do not activate `.venv` manually; use `uv run`.
- **GitHub authentication fails on push:** authenticate using Git Credential Manager or GitHub CLI; do not embed access tokens in remote URLs.

## Next milestone
Implement `GET /health` returning `{"status":"healthy","service":"fde-incident-assistant"}`, add pytest coverage, run Ruff and commit code. See [Day 2 commands](day02-command-reference.md).

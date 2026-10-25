# Day 2 — Command Reference (Windows and macOS)

**Purpose:** A copy/paste-friendly checklist of commands for setting up the FDE repository, verifying tools, creating a uv-managed Python project, installing API/test dependencies, and committing work. Full explanations: [Development stack and installation](day02-development-stack-and-installation.md).

## A. Windows PowerShell — on this laptop (tools already installed)
```powershell
# 1. Confirm E: drive is available
Test-Path E:\

# 2. Clone repository only if E:\FDE does not already exist
Set-Location E:\
git clone https://github.com/TEJAPS/FDE.git
Set-Location E:\FDE
code .

# 3. Check repo and Python versions
git status
python --version
py --version
py -0p
uv --version

# 4. Pin and initialize Python 3.13
uv python pin 3.13
# Run ONLY if pyproject.toml does not exist:
uv init --bare --python 3.13
uv sync

# 5. Add application and development dependencies
uv add fastapi "uvicorn[standard]"
uv add --dev pytest httpx ruff

# 6. Verify project tools
uv run python --version
uv run python -c "import fastapi; print(fastapi.__version__)"
uv run pytest --version
uv run ruff --version

# 7. Optional Docker daemon check
docker info
```

**Already cloned?** Replace the clone block with:
```powershell
Set-Location E:\FDE
git status
git pull --ff-only
code .
```

**Project already initialized?** Skip `uv init --bare`. Do not overwrite existing `pyproject.toml`.

## B. macOS Terminal — fresh setup
```bash
# Homebrew must already be installed; see https://brew.sh/
brew install python@3.13 git uv
brew install --cask visual-studio-code docker-desktop

# Verify tools
python3.13 --version
git --version
uv --version
docker --version

# Clone into ~/dev/FDE
mkdir -p ~/dev
cd ~/dev
git clone https://github.com/TEJAPS/FDE.git
cd FDE

# Project setup
uv python pin 3.13
# Only if pyproject.toml is absent:
uv init --bare --python 3.13
uv sync
uv add fastapi "uvicorn[standard]"
uv add --dev pytest httpx ruff

# Verify
uv run python --version
uv run python -c "import fastapi; print(fastapi.__version__)"
uv run pytest --version
uv run ruff --version
docker info
```

## C. Useful everyday commands (both OSes)
```text
git status
git pull --ff-only
uv sync
uv run python --version
uv run pytest -q
uv run ruff check .
uv run ruff format --check .
git add .
git commit -m "feat: initialize FastAPI incident assistant"
git push origin main
```

Run `git add .` only after checking `git status` and ensuring no secrets, local environments or generated artifacts are staged. Do not commit until files exist and tests pass.

## D. Next-step application commands (after creating app files)
```text
uv run uvicorn app.main:app --reload
uv run pytest -q
uv run ruff check .
uv run ruff format --check .
```
Open http://127.0.0.1:8000/health after the application has been implemented. Do not expect these commands to succeed before `app/main.py` and tests are created.

## E. Environment variables and paths
| Item | Windows | macOS |
|---|---|---|
| Repo | `E:\FDE` | `~/dev/FDE` |
| Virtualenv | `E:\FDE\.venv` | `~/dev/FDE/.venv` |
| Interpreter | `.venv\Scripts\python.exe` | `.venv/bin/python` |
| Project Python | `3.13` | `3.13` |
| Dev server | `http://127.0.0.1:8000` | same |

## F. GitHub tracking rules
Commit `pyproject.toml`, `uv.lock`, `.python-version`, code, tests and documentation. Exclude `.venv/`, caches, `.env` and credentials.

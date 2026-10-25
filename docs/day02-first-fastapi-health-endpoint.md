# Day 2 — First FastAPI Endpoint: Implementation Walkthrough

**Status:** Instructions documented; awaiting learner's local execution and test results. **Working directory:** `E:\FDE`.

## Purpose
Build a minimal, testable Python REST service and understand how uv, Uvicorn, FastAPI routing and pytest work together. This is the first implementation step of the FDE Incident Assistant.

## Knowledge check
**Question:** A new engineer clones the repository. Which command normally recreates its declared and locked Python dependency environment?

- A. `uv init --bare` — initializes project metadata, not the correct post-clone step.
- **B. `uv sync` — correct.** Creates/synchronizes the project virtual environment with declared and locked dependencies.
- C. `pip install fastapi` — installs a single package but doesn't restore the project's full lockfile.
- D. `uv python pin 3.13` — records the Python version preference; doesn't synchronize packages.

**Learner's answer: B — correct.**

## Verified environment
- Python in uv environment: **3.13.5**
- FastAPI: **0.162.0**
- pytest: **9.1.1**
- Ruff: **0.17.2**

## Target API contract
`GET http://127.0.0.1:8000/health` → HTTP 200:

```json
{"status":"healthy","service":"fde-incident-assistant"}
```

This is a **liveness** check, not a dependency readiness check.

## Architecture
```text
Browser / PowerShell / API client
           |
       GET /health
           v
Uvicorn HTTP/ASGI server :8000
           |
           v
FastAPI route registry
           |
           v
@app.get("/health") -> health_check()
           |
           v
HTTP 200 JSON response
```

## Step 1 — Create folders
```powershell
Set-Location E:\FDE
New-Item -ItemType Directory -Force app, tests
New-Item -ItemType File -Force app\__init__.py
New-Item -ItemType File -Force tests\__init__.py
```

The `__init__.py` files explicitly mark packages for imports. Create `app/main.py` and `tests/test_health.py` next.

## Step 2 — Application code: `app/main.py`
```python
from fastapi import FastAPI

app = FastAPI(
    title="FDE Incident Assistant",
    version="0.1.0",
    description="Enterprise AI incident management API",
)


@app.get("/health")
def health_check() -> dict[str, str]:
    return {
        "status": "healthy",
        "service": "fde-incident-assistant",
    }
```

- `FastAPI(...)` creates the ASGI app and API metadata.
- `@app.get("/health")` registers an HTTP GET route (roughly analogous to Spring `@GetMapping`).
- `health_check()` returns a Python dict; FastAPI serializes it as JSON.
- `dict[str, str]` annotates the return shape; it's not a substitute for full runtime response-model validation.

## Step 3 — Start the server
```powershell
uv run uvicorn app.main:app --reload
```
`uv run` selects the project environment. `app.main:app` means import variable `app` from module `app.main`. `--reload` is development-only.

In a **second terminal**:
```powershell
Invoke-RestMethod http://127.0.0.1:8000/health
```
Optional browser: http://127.0.0.1:8000/docs (Swagger UI). Do not expect it to work before the server is running.

## Step 4 — Tests: `tests/test_health.py`
```python
from fastapi.testclient import TestClient

from app.main import app

client = TestClient(app)


def test_health_returns_200():
    response = client.get("/health")
    assert response.status_code == 200


def test_health_response_body():
    response = client.get("/health")
    assert response.json() == {
        "status": "healthy",
        "service": "fde-incident-assistant",
    }
```

`TestClient` exercises the ASGI app without a separately running Uvicorn process, similar conceptually to Spring MockMvc.

## Step 5 — Run automated checks
```powershell
uv run pytest -q
uv run ruff check .
uv run ruff format --check .
```

If formatting needs changes:
```powershell
uv run ruff format .
uv run ruff format --check .
```

**Expected**, not yet verified: 2 passing tests and clean lint/format checks.

## Step 6 — Review changes and prepare for Git
```powershell
git status
```
Track `app/`, `tests/`, `.python-version`, `pyproject.toml`, `uv.lock`, and documentation. Ignore `.venv/`, `__pycache__/`, `.pytest_cache/`, `.ruff_cache/`, `.env` and secrets. Inspect staged changes before committing.

## Checkpoint and next steps
1. Learner creates local files and runs API.
2. Learner shares the HTTP response, pytest result and Ruff output.
3. Troubleshoot if needed; only then mark implementation complete.
4. Commit tested code to GitHub, update Day 2 documentation and proceed to the next concept.

**Important:** Documentation being committed does **not** imply that local application code was executed, tested or pushed.

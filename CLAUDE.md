# CLAUDE.md — Mergington High School Activities API

## Project Overview

This repository is a **GitHub Skills exercise** ("Getting Started with GitHub Copilot") that uses a simple FastAPI web application as its teaching vehicle. The app — the **Mergington High School Activities API** — lets students view and sign up for extracurricular activities.

The exercise guides learners through five progressive steps that demonstrate different GitHub Copilot features (inline suggestions, Ask/Edit/Agent modes, inline chat, and Copilot on GitHub.com). All exercise instructions live in `.github/steps/` and are surfaced through GitHub Actions → GitHub Issues.

---

## Repository Structure

```
.
├── src/
│   ├── app.py              # FastAPI application (sole backend entry point)
│   └── static/
│       ├── index.html      # Single-page frontend
│       ├── app.js          # Vanilla JS — fetches activities & handles signup form
│       └── styles.css      # Stylesheet
├── .devcontainer/
│   └── devcontainer.json   # Dev container: Python 3.13, port 8000, installs requirements
├── .github/
│   ├── steps/              # Markdown content for each exercise step (1–5 + review)
│   └── workflows/          # GitHub Actions that drive exercise progression (0–5 + start)
├── .vscode/
│   └── launch.json         # VS Code debug config: launches uvicorn with --reload
├── pytest.ini              # Sets pythonpath = . so `src` is importable
├── requirements.txt        # fastapi, uvicorn
├── .gitignore
├── LICENSE
└── README.md
```

---

## Technology Stack

| Layer     | Technology                          |
|-----------|-------------------------------------|
| Backend   | Python 3.13, FastAPI, Uvicorn       |
| Frontend  | Vanilla HTML / CSS / JavaScript     |
| Testing   | pytest (configured via `pytest.ini`)|
| Dev env   | GitHub Codespaces / VS Code devcontainer |

---

## Running the Application

### Install dependencies
```bash
pip install -r requirements.txt
```

### Start the server (from repo root)
```bash
uvicorn src.app:app --reload
```

The app is served at `http://localhost:8000`.

- **UI**: `http://localhost:8000/` (redirects to `/static/index.html`)
- **Swagger docs**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`

### VS Code launch config
The `.vscode/launch.json` config "Launch Mergington WebApp" runs the same uvicorn command with `--reload` automatically. Use **Run and Debug → Start Debugging** (F5).

---

## API Endpoints

| Method | Endpoint                                        | Description                        |
|--------|-------------------------------------------------|------------------------------------|
| GET    | `/`                                             | Redirects to the static frontend   |
| GET    | `/activities`                                   | Returns all activities as JSON     |
| POST   | `/activities/{activity_name}/signup?email=...`  | Signs a student up for an activity |

### Data Model

Activities are stored **in-memory** as a Python dict (keyed by activity name). Data resets on every server restart.

```python
{
    "Chess Club": {
        "description": str,
        "schedule": str,
        "max_participants": int,
        "participants": list[str]   # list of student email addresses
    },
    ...
}
```

Students are identified by their `@mergington.edu` email address.

---

## Key Conventions

### Python / Backend
- All application code lives in `src/app.py`. Keep it self-contained unless the exercise step explicitly asks for new files.
- The `pytest.ini` sets `pythonpath = .` so you can import `src.app` from the repo root. Always run pytest from the repo root.
- HTTP errors use FastAPI's `HTTPException` with appropriate status codes (404 for missing activity, 400 for duplicate signup).
- No database — all state is held in the `activities` dict. This is intentional for simplicity.

### Frontend
- `app.js` communicates with the API using the Fetch API. Activity names in URLs are `encodeURIComponent`-encoded.
- `index.html` is fully static; JavaScript populates the activity list and form select on `DOMContentLoaded`.
- No build step, no bundler — plain files served by FastAPI's `StaticFiles` mount.

### Git / Branch workflow
- The exercise expects a working branch named **`accelerate-with-copilot`** for pushes that trigger GitHub Actions grading.
- The Claude development branch is `claude/claude-md-mm34l73i45vl15px-bgnpE`.
- Do not push directly to `main` or `master`.

### GitHub Actions (exercise infrastructure)
- Workflows in `.github/workflows/` are numbered `0-start-exercise.yml` through `5-copilot-on-github.yml`.
- Each workflow is triggered by specific events (push to a branch, PR creation, issue comment, etc.) and grades the corresponding exercise step.
- Step content (instructions shown in GitHub Issues) is sourced from `.github/steps/`.
- Do not modify workflow files or step markdown files unless explicitly instructed — these are the exercise grading infrastructure.

---

## Running Tests

```bash
pytest
```

Run from the repo root. The `pythonpath = .` in `pytest.ini` makes `src` importable as a package.

> **Note:** The starter repo does not ship test files. Exercise step 4 (Copilot Agent Mode) asks learners to use Copilot to generate tests. If you add tests, place them in a `tests/` directory at the repo root following pytest naming conventions (`test_*.py` / `*_test.py`).

---

## Development Environment

The `.devcontainer/devcontainer.json` defines:
- **Image**: `mcr.microsoft.com/vscode/devcontainers/python:3.13`
- **Port forwarding**: 8000
- **Post-create command**: `pip install -r requirements.txt` (auto-installs dependencies)
- **VS Code extensions**: GitHub Copilot, Python (`ms-python.python`), Debugpy (`ms-python.debugpy`)

Opening this repo in a GitHub Codespace or VS Code Dev Container will give a fully configured environment automatically.

---

## Exercise Step Summary

| Step | Topic                        | Key Copilot Feature              |
|------|------------------------------|----------------------------------|
| 1    | Hello Copilot                | Ask Mode (`@workspace`), Terminal Inline Chat |
| 2    | Fix a bug + add sample data  | Inline suggestions, Inline Chat (Edit) |
| 3    | Copilot Edits                | Edit Mode (multi-file edits)     |
| 4    | Copilot Agent Mode           | Agent Mode (autonomous task execution) |
| 5    | Copilot on GitHub            | Copilot in GitHub PR/Issue UI    |

---

## AI Assistant Notes

- **Primary file to modify**: `src/app.py` — all exercise coding tasks target this file unless otherwise specified.
- **Static files** (`src/static/`) may be modified in later steps (Copilot Edits / Agent Mode exercises).
- **Do not modify** `.github/workflows/` or `.github/steps/` files — they are exercise grading infrastructure.
- The app uses **in-memory storage**; there is no migration, seed script, or persistence layer to worry about.
- When adding a new endpoint, follow the existing FastAPI pattern: define a route function, use `HTTPException` for errors, return a plain dict.
- When writing tests, import the FastAPI `app` object from `src.app` and use `fastapi.testclient.TestClient`.

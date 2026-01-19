---
icon: lucide/code
---

# Development

This page covers local development workflows for backend and frontend.

## Backend

- Use a virtual environment or your preferred Python environment manager (poetry, pyenv, venv).
- Install dev dependencies and run tests with `pytest`.
- Lint and type-check with `ruff` and `mypy` (if present in the repo).

Common commands (adjust to your environment manager):

```bash
# in src/backend
python -m venv .venv
.venv\Scripts\activate
pip install -e .[dev]
pytest
# linting
ruff check .
# type checking
mypy
```

### Database migrations

If the project uses Alembic for migrations:

```bash
# generate a migration
alembic revision --autogenerate -m "describe change"
# apply migrations
alembic upgrade head
```

## Frontend

```bash
cd frontend
npm install
npm run dev
# to build for production
npm run build
```

## Background workers

To run Celery workers locally (example):

```bash
# from repo root
celery -A src.backend.app.celery worker -l info
```

---

If you want, I can add precise commands from your repo (test runner, lint config, and exact Celery app location) — provide file paths or confirm and I’ll update these instructions.
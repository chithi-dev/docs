---
icon: lucide/rocket
---

# Getting Started

## Prerequisites

- Git
- Docker & Docker Compose
- Node.js (for frontend development)
- Python 3.10+ (for backend development)

## Quickstart (recommended)

1. Clone the repository:

```bash
git clone https://github.com/<owner>/chithi.git
cd chithi
```

2. Start the full stack with Docker Compose:

```bash
docker-compose up --build
```

3. Visit the app in your browser (default: http://localhost)

## Developer Quickstart

- To run the backend only (development):

```bash
# from repo root
cd src/backend
# create venv, install deps (poetry/pip)
python -m venv .venv
.venv\Scripts\activate
pip install -e .[dev]
# run migrations (if applicable)
# alembic upgrade head
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

- To run the frontend only:

```bash
cd frontend
npm install
npm run dev
```

## Verify

- Backend API docs: http://localhost:8000/docs (FastAPI interactive docs)
- Frontend: http://localhost:5173 (or as configured by the frontend dev server)


---

If anything above doesn't work for you, tell me which step failed and I’ll adjust the docs.
---
icon: lucide/layers
---

# Architecture

Chithi is composed of several components:

- **Backend** — FastAPI application providing the REST/OpenAPI surface and background task integration.
- **Frontend** — Svelte/SvelteKit app serving the user interface.
- **Proxy** — Caddy is used as a reverse proxy (in production or containerized stacks).
- **Worker** — Celery (or similar) for background jobs and asynchronous tasks.
- **Database** — a relational database (e.g. PostgreSQL) configured via environment variables in `docker-compose`.

```mermaid
graph LR
  Frontend[Frontend (Svelte)] -->|API| Backend[Backend (FastAPI)]
  Backend -->|Tasks| Worker[Celery Worker]
  Backend -->|Data| DB[(Database)]
  Frontend -->|Proxied via| Caddy[Caddy Proxy]
``` 

Notes:
- Components are containerized for consistent local and production deployments.
- Environment variables and service configuration live in `docker-compose.yml` and the services' respective `Dockerfile`s.
---
icon: lucide/box
---

# Deployment & CI

This project includes Dockerfiles and `docker-compose.yml` files to run the stack locally and in production-like environments.

## Local deployment (Docker Compose)

```bash
# build and run
docker-compose up --build -d
# stop
docker-compose down
```

For development there may be `docker-compose-dev.yml` with services and mounts better suited to an iterative dev workflow.

## Environment variables

The services are configured via environment variables. Add a `.env` or `.env.local` (not committed) to document 
secrets and configuration values.

## CI / GitHub Actions

The repository contains GitHub Actions workflows to build and publish container images and run tests.
You can adapt those workflows to publish the docs or deploy to your environment (GHCR, DockerHub, Kubernetes, etc.).

## Production considerations

- Use secrets management for DB credentials, API keys, and other secrets.
- Consider using an external DB service with backups and managed storage for production.
- Use TLS certificates (Caddy can manage them automatically) and configure secure headers, HSTS, and rate limiting.

If you want, I can add a sample GH Actions workflow to build and publish images and optionally deploy to a host or a container registry.
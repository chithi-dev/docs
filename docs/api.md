---
icon: lucide/server-cog
---

# API Reference

The backend is implemented with **FastAPI** and exposes an OpenAPI schema and interactive docs.

- Interactive Swagger UI: `GET /docs`
- Redoc (if enabled): `GET /redoc`
- OpenAPI JSON: `GET /openapi.json`

## Export the OpenAPI schema

To export a static copy of the API schema:

```bash
curl -s http://localhost:8000/openapi.json -o openapi.json
```

You can use `openapi.json` with tools like `redoc-cli`, Swagger Editor, or `openapi-generator` to produce reference documentation, client SDKs, or server stubs.

## Regenerating docs

If you change or add endpoints, restart the server and re-export the `openapi.json` if you use a static API reference.

If you'd like, I can add a small GitHub Action to automatically publish the OpenAPI JSON and a generated Redoc page.
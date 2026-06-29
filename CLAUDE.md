# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Structure

- `Dockerfile` — Builds from `ghcr.io/open-webui/open-webui:main`, the official Open WebUI image.
- `.gitignore` — Ignored by default; the base image handles all dependencies.

## GitHub Container Registry (GHCR)

Pushing to `main` triggers an automatic CI build that pushes the image to GHCR. You must configure a PAT secret for this to work:

1. Create a **classic** PAT at https://github.com/settings/tokens (not fine-grained — the `packages` scope isn't available on fine-grained tokens). Tick **`packages`**.
2. Add it as a repository secret named `GHCR_PAT` under **Settings → Secrets and variables → Actions**.

The workflow uses this PAT to authenticate Docker pushes to `ghcr.io/simplylimitless/homelab-openweb-ui:latest` and `ghcr.io/simplylimitless/homelab-openweb-ui:<run_number>`. The automatic `GITHUB_TOKEN` does not grant GHCR package write permissions.

## How to run

```bash
# 1. Build locally
docker build -t homelab-openweb-ui .

# 2. Run with data persistence (mounted at /app/backend/data)
docker run -p 3000:8080 \
  --name openwebui \
  -v $(pwd)/data:/app/backend/data \
  -e OPENAI_API_KEY=sk-your-key \
  -e OLLAMA_BASE_URL=http://your-ollama-host:11434 \
  homelab-openweb-ui
```

Browse to `http://localhost:3000`.

## Configuration

Open WebUI stores all persistent data — settings, database files, uploads — under `/app/backend/data` inside the container. Always volume-mount this path to avoid data loss on restart or update.

Key environment variables:

| Variable | Description |
|---|---|
| `OPENAI_API_KEY` | API key for OpenAI-compatible model providers |
| `OLLAMA_BASE_URL` | Base URL for Ollama model serving (e.g. `http://host:11434`) |
| `HF_HUB_OFFLINE` | Set to `1` in air-gapped environments to block external model downloads |

See the [Open WebUI repo](https://github.com/open-webui/open-webui) for the full environment variable reference.

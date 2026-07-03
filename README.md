# homelab-openweb-ui

Open WebUI — a self-hosted conversational AI interface. Docker-based, using the official `open-webui:main` image.

## Structure

- `Dockerfile` — Builds from `ghcr.io/open-webui/open-webui:main`, the official Open WebUI image.
- `.gitignore` — Ignored by default; the base image handles all dependencies.
- `docker-compose.yml` — Pre-configured Compose file with persistent volume and environment variables.

## Quick start

Pull the prebuilt image and run with docker compose:

```bash
cp .env.example .env   # edit with your API keys, Ollama URL, etc.
docker compose up -d
```

Browse to `http://localhost:3000`.

### Build locally instead

Uncomment the `build:` line in `docker-compose.yml` (and comment out the `image:` line):

```bash
docker compose up -d --build
```

## Configuration

Open WebUI stores all persistent data — settings, database files, uploads — under `/app/backend/data` inside the container. The Compose file uses a named volume (`oww-data`) so your data survives container restarts and updates.

Environment variables are read from `.env` (copy `.env.example`). Key variables:

| Variable | Description | Default |
|---|---|---|
| `OPENAI_API_KEY` | API key for OpenAI-compatible model providers | _(empty)_ |
| `OLLAMA_BASE_URL` | Base URL for Ollama model serving | `http://host.docker.internal:11434` |
| `HF_HUB_OFFLINE` | Set to `1` in air-gapped environments to block external downloads | `0` |

See the [Open WebUI repo](https://github.com/open-webui/open-webui) for the full environment variable reference.

## Manual docker run

If you prefer not to use Compose:

```bash
docker pull ghcr.io/simplylimitless/homelab-openweb-ui:latest
docker run -p 3000:8080 \
  --name openwebui \
  -v $(pwd)/data:/app/backend/data \
  -e OPENAI_API_KEY=sk-your-key \
  -e OLLAMA_BASE_URL=http://your-ollama-host:11434 \
  ghcr.io/simplylimitless/homelab-openweb-ui:latest
```

## CI/CD

Pushing to `main` triggers an automatic build (see [`.github/workflows/docker.yml`](.github/workflows/docker.yml)). Each push is tagged with both `latest` and a numeric build ID for rollback:

```bash
docker pull ghcr.io/simplylimitless/homelab-openweb-ui:3
```

**GHCR write permission:** The workflow uses a PAT stored in the repository secret `GHCR_PAT` to push to GitHub Container Registry. Create one at https://github.com/settings/tokens (classic tokens, not fine-grained — that scope isn't available there) with the **`packages`** scope ticked, then add it as a repo secret named `GHCR_PAT`. The automatic `GITHUB_TOKEN` doesn't grant GHCR package write permissions, so this PAT is required.

> **Note:** After setting up the `GHCR_PAT` secret, re-run the workflow from the Actions tab or push a test commit if you don't see the image published yet.

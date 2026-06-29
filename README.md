# homelab-openweb-ui

Open WebUI — a self-hosted conversational AI interface. Docker-based, using the official `open-webui:main` image.

## Quick start

### Deploy from GitHub Container Registry

```bash
docker login ghcr.io -u YOUR_GITHUB_USERNAME   # password: a GitHub PAT with `packages` scope (create one at https://github.com/settings/tokens, not the fine-grained type)
docker pull ghcr.io/simplylimitless/homelab-openweb-ui:latest
```

### Or build locally

```bash
docker build -t homelab-openweb-ui .
```

See [CLAUDE.md](CLAUDE.md) for the full run command with configuration.

Browse to `http://localhost:3000`.

> **Note:** After setting up the `GHCR_PAT` secret, re-run the workflow from the Actions tab or push a test commit if you don't see the image published yet.

### CI/CD

Pushing to `main` triggers an automatic build (see [`.github/workflows/docker.yml`](.github/workflows/docker.yml)). Each push is tagged with both `latest` and a numeric build ID for rollback:

```bash
docker pull ghcr.io/simplylimitless/homelab-openweb-ui:3
```

**GHCR write permission:** The workflow uses a PAT stored in the repository secret `GHCR_PAT` to push to GitHub Container Registry. Create one at https://github.com/settings/tokens (classic tokens, not fine-grained — that scope isn't available there) with the **`packages`** scope ticked, then add it as a repo secret named `GHCR_PAT`. The automatic `GITHUB_TOKEN` doesn't grant GHCR package write permissions, so this PAT is required.

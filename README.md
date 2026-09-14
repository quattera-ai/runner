# Quattera CI runners — Docker Compose

Run one or more Quattera **CI runners** (the Linux agent in headless mode, with Chrome inside) on any Linux host with Docker.

Image: `ghcr.io/quattera-ai/agent` · Guide: *Quattera CI/CD Integration Guide*, §3.2

## Quick start

1. In Quattera: **Settings → CI/CD → + New runner** — one runner per parallel job (`ci-runner-1`, `ci-runner-2`, …). Copy each runner's token (shown once).
2. On the host:

   ```sh
   git clone https://github.com/quattera-ai/runner.git && cd runner
   cp .env.example .env      # AGENT_TOKEN_1=…, AGENT_TOKEN_2=…
   docker compose up -d
   docker compose logs -f ci-runner-1
   ```

3. Back in **Settings → CI/CD** each runner turns **● Online** within a minute.

## Day to day

| Task | Command |
|---|---|
| Update to a new agent version | `docker compose pull && docker compose up -d` |
| Pin a version | `QUATTERA_AGENT_TAG=2.0.94` in `.env` |
| Add a third runner | copy a service block in `docker-compose.yml` (`ci-runner-3`, `AGENT_TOKEN_3`, its own volume) |
| Stop, keep runner identities | `docker compose down` |
| Stop and forget identities | `docker compose down -v` |

Runners in containers never update themselves — the image tag is the version.

## Security defaults (keep them)

Non-root user, read-only root filesystem, all Linux capabilities dropped, no published ports, Chrome's own sandbox kept on (`chrome-seccomp.json` allows only the namespace calls that sandbox needs — do not replace it with `--no-sandbox`). The runner token lives only in `.env`; keep that file out of version control.

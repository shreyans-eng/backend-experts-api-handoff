# Frontend team — Coinzy Experts API (local)

Use this handoff repo to run the backend API locally. You do **not** get backend source code — only Docker Compose and a pre-built image.

**API base URL:** `http://localhost:3000`

---

## What you need from backend

1. Access to this GitHub repo (invite).
2. Permission to pull the Docker image:  
   `ghcr.io/trackzio-mobile-apps/coinzy-experts-api`
3. Dev-only values for:
   - `USER_JWT_SHARED_SECRET`
   - `EXPERT_JWT_SECRET`
   - `ADMIN_API_KEY`
   - `INTERNAL_EXPERT_EMAIL` / `INTERNAL_EXPERT_PASSWORD` (if you need expert login)

Never use production secrets.

---

## One-time setup

### 1. Install Docker Desktop

https://www.docker.com/products/docker-desktop/  
Start Docker and wait until it is running.

### 2. Log in to GitHub Container Registry (to pull the API image)

```bash
echo YOUR_GITHUB_PAT | docker login ghcr.io -u YOUR_GITHUB_USERNAME --password-stdin
```

Use a GitHub Personal Access Token with `read:packages` (and org SSO authorized if required). Backend can help if pull fails with `unauthorized`.

### 3. Clone and start

```bash
git clone https://github.com/Trackzio-Mobile-Apps/coinzy-experts-api-handoff.git
cd coinzy-experts-api-handoff

cp .env.example .env
# Paste the auth secrets backend gave you into .env

docker compose up -d
```

First run may take a minute while Mongo/Redis/API start.

---

## Verify it works

| Check | URL |
| --- | --- |
| Health | http://localhost:3000/health → should show `OK` |
| API docs | http://localhost:3000/docs |

Useful commands:

```bash
docker compose logs -f api    # API logs
docker compose ps             # container status
docker compose down           # stop everything
```

---

## Point your frontend at the API

Set your app’s API base URL to:

```text
http://localhost:3000
```

Example: `.env` / Vite / Expo config — whatever your FE uses for `API_URL` / `BASE_URL`.

---

## When backend releases a new version

You never rebuild the backend image. Pull updates only:

```bash
cd coinzy-experts-api-handoff
git pull
docker compose pull
docker compose up -d
```

See `CHANGELOG.md` and the `image:` line in `docker-compose.yml` for the current version (e.g. `v1.0.0`).

---

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `port is already allocated` on 3000 | Stop whatever is using port 3000, then `docker compose up -d` again |
| `unauthorized` pulling the image | Re-run `docker login ghcr.io` with a PAT that has package read access |
| API exits / JSON env error | Use `.env` copied from `.env.example`; do not wrap the slab JSON in quotes |
| `/docs` redirects | Open http://localhost:3000/docs/ (trailing slash) or follow the redirect |

---

## Do not

- Ask for or clone the private backend source repo for UI work.
- Rebuild the API Docker image.
- Commit your `.env` file.
- Point this stack at production databases or secrets.

# Coinzy Experts API — Frontend Handoff

Run the Coinzy Experts backend API locally with Docker. **No backend source code** is included — only a pre-built image and Compose config.

API base URL: `http://localhost:3000`

**Share with FE:** send them [`FE-SHARE.md`](./FE-SHARE.md) (short setup guide).

---

## Prerequisites

1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) and start it.
2. Ask the backend team for:
   - Access to this handoff repo (if private)
   - Permission to pull `ghcr.io/trackzio-mobile-apps/coinzy-experts-api`
   - Dev values for auth secrets (see **Environment** below)

---

## Quick start

```bash
git clone https://github.com/shreyans-eng/coinzy-experts-api-handoff.git
cd coinzy-experts-api-handoff

cp .env.example .env
# Edit .env — at minimum set the auth secrets the backend team gave you

docker compose up -d
```

### Verify

- Health: http://localhost:3000/health
- Docs: http://localhost:3000/docs

### Frontend

Point your app’s API base URL to:

```text
http://localhost:3000
```

### Useful commands

```bash
# Logs
docker compose logs -f api

# Stop
docker compose down

# Stop and remove DB volume (fresh Mongo)
docker compose down -v
```

---

## Environment

`.env.example` is safe to commit. Copy it to `.env` (gitignored).

| Required for local UI | Notes |
| --- | --- |
| `USER_JWT_SHARED_SECRET` | Must match the secret used to mint mobile-user JWTs |
| `EXPERT_JWT_SECRET` | Expert login tokens |
| `ADMIN_API_KEY` | Admin `x-admin-key` header |
| `INTERNAL_EXPERT_*` | Seed/internal expert credentials (if used) |

Leave IAP / AWS / Firebase / Grafana blank unless you are testing those flows. Get real **dev** values from the backend team — never use production secrets.

`MONGODB_URI` and `REDIS_URL` in `.env.example` already use Docker service names (`mongo`, `redis`). Do not change them to `localhost` while using Compose.

---

## Pulling a new backend version

**Frontend never rebuilds the API image.** Backend publishes a new tagged image; you only pull it.

```bash
git pull
docker compose pull
docker compose up -d
```

Check `CHANGELOG.md` and the `image:` tag in `docker-compose.yml` for the current version (e.g. `v1.0.0`).

---

## How updates work (backend → frontend)

```text
Backend code (private repo)
        ↓
Build Docker image
        ↓
Tag version (v1.0.1, v1.1.0, …)
        ↓
Push to private registry (GHCR)
        ↓
Update image tag + CHANGELOG in this handoff repo
        ↓
FE: git pull && docker compose pull && docker compose up -d
        ↓
Frontend uses http://localhost:3000
```

### Backend release checklist (for API team)

1. Change code in the private backend repo; run tests/lint.
2. Build and tag:

   ```bash
   docker build -t ghcr.io/trackzio-mobile-apps/coinzy-experts-api:vX.Y.Z .
   docker push ghcr.io/trackzio-mobile-apps/coinzy-experts-api:vX.Y.Z
   ```

3. In this handoff repo: set `image:` to the new tag, update `CHANGELOG.md`, commit, push.
4. Tell FE to pull the handoff repo and run `docker compose pull && docker compose up -d`.

Never retag an existing version with different code. Bump the version instead.

---

## Registry access

Images live at:

```text
ghcr.io/trackzio-mobile-apps/coinzy-experts-api:<version>
```

If pull fails with `unauthorized`, log in (backend team grants package access):

```bash
echo YOUR_GITHUB_PAT | docker login ghcr.io -u YOUR_GITHUB_USERNAME --password-stdin
```

Use a GitHub PAT with `read:packages` (and SSO authorized for the org if required).

---

## Notes

- Stack: API + MongoDB 7 + Redis 7.
- Many routes may still return `501` while the backend is under active development; use `/docs` for the current surface.
- Do not commit `.env` or production secrets.

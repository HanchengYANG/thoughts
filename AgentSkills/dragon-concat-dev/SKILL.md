---
name: dragon-concat-dev
description: Local development workflow for /home/hancheng/project_dragon_concat.
version: 1.0.1
author: Hermes Agent
license: MIT
tags: [nuxt, fastapi, alembic, postgres, local-dev]
triggers:
  - work in /home/hancheng/project_dragon_concat
  - dragon concat
  - project_dragon_concat
---

# Dragon Concat Dev

Use this skill when working inside `/home/hancheng/project_dragon_concat`.

## Scope

This skill is for:

- local development startup/shutdown
- backend/frontend test workflow
- repo structure orientation
- avoiding common workflow mistakes

## Repo shape

- `frontend/` — Nuxt app
- `backend/` — FastAPI + SQLAlchemy + Alembic
- `deploy/docker-compose.yml` — local PostgreSQL helper
- `scripts/dev-stack.sh` — preferred app lifecycle helper
- `docs/` — feature and UI design notes
- `example/` — historical reference only

## Preferred local workflow

Work from repo root.

### Check service status

```bash
bash scripts/dev-stack.sh status
```

### Start backend + frontend

```bash
bash scripts/dev-stack.sh start
```

### Logs

```bash
bash scripts/dev-stack.sh logs
bash scripts/dev-stack.sh logs backend
bash scripts/dev-stack.sh logs frontend
```

### Stop or restart

```bash
bash scripts/dev-stack.sh stop
bash scripts/dev-stack.sh restart
```

## Local database

If PostgreSQL is not already running, start it with:

```bash
docker compose -f deploy/docker-compose.yml up -d db
```

## Backend workflow

Run commands from `backend/`.

### Common commands

```bash
source .venv/bin/activate
alembic upgrade head
pytest
```

### Important test behavior

Backend tests:

- create a temporary PostgreSQL database per session
- run Alembic migrations against that temporary DB
- reuse the same PostgreSQL server instead of a separate test container
- truncate and reseed tables between tests

Implication: PostgreSQL must be reachable before running backend tests.

## Frontend workflow

Run commands from `frontend/`.

### Common commands

```bash
npm run dev
npm run test
npm run build
```

### Important test behavior

Frontend tests use:

- `vitest run`
- `happy-dom`
- custom Nuxt auto-import mocking in `frontend/vitest.config.ts`

## Recommended execution pattern

For most tasks:

1. Read the task-relevant docs and files first
2. Check whether backend, frontend, DB, or docs are affected
3. Use `scripts/dev-stack.sh` before manually managing app processes
4. Make the smallest change that fits the current architecture
5. Run targeted tests for the affected area
6. Manually verify changed routes when UI behavior is involved

## Common pitfalls

- forgetting PostgreSQL before backend tests
- manually starting backend/frontend first instead of using `scripts/dev-stack.sh`
- treating `example/` as the current implementation source
- skipping manual browser verification after UI changes
- over-scoping simple asset replacement requests into redesign work

## Asset replacement note: splash reveal image

For the customer-side full-screen splash/reveal animation at `/`, the active image is `frontend/public/reveal-food.jpg`.

Current references:

- `frontend/app/pages/index.vue` preloads `/reveal-food.jpg`
- `frontend/app/components/LoginRevealOverlay.vue` renders `/reveal-food.jpg`

So when the task is just "replace the root animation image" or similar, the minimal correct change is:

1. replace `frontend/public/reveal-food.jpg`
2. do not redesign `/menu`
3. do not add extra generated image assets unless explicitly requested

Also distinguish two different splash requests before changing anything:

- **image replacement** → edit `frontend/public/reveal-food.jpg`
- **text above the splash layer** → edit `frontend/app/components/LoginRevealOverlay.vue`

In particular, the centered splash brand text is not baked into the overlay component's logic elsewhere; it comes from the `reveal-brand` / `reveal-brand__text` markup and styles inside `LoginRevealOverlay.vue`.

## Production deploy notes

Current verified production setup:

- SSH alias: `zuolinyouli-v1`
- remote repo path: `/home/ubuntu/dragon_concat`
- deploy helper: `scripts/prod-deploy.sh`

Useful production commands:

```bash
ssh zuolinyouli-v1
cd /home/ubuntu/dragon_concat

# pull + rebuild backend/frontend + run alembic + restart app containers
bash scripts/prod-deploy.sh auto

# if already pulled, rebuild/restart without git pull
bash scripts/prod-deploy.sh deploy

# inspect status/logs
bash scripts/prod-deploy.sh ps
bash scripts/prod-deploy.sh logs
```

Behavior of `prod-deploy.sh`:

- `auto` = `git pull` + build backend/frontend + `alembic upgrade head` + `compose up -d backend frontend`
- `deploy` = same as above, but without `git pull`

Verification tips on prod:

```bash
# front door
curl -I -s https://zuolinyouli.com/

# app health through frontend proxy
curl -s https://zuolinyouli.com/api/v1/system/health

# splash image currently used by the root reveal animation
curl -I -s https://zuolinyouli.com/reveal-food.jpg
```

## Quick command cheat sheet

```bash
# repo root
bash scripts/dev-stack.sh status
bash scripts/dev-stack.sh start
bash scripts/dev-stack.sh logs
bash scripts/dev-stack.sh stop

# postgres
docker compose -f deploy/docker-compose.yml up -d db

# backend
cd backend
source .venv/bin/activate
alembic upgrade head
pytest

# frontend
cd frontend
npm run dev
npm run test
npm run build
```

# Fullstack Webapp (Docker)

## Overview

This project includes:
- Frontend: Next.js
- Backend: NestJS
- Database: PostgreSQL
- Orchestration: Docker Compose

## 1) Requirements

Install these tools first:
- Docker Desktop (latest recommended)
- Git

Windows note:
- Enable File Sharing for the drive that contains this project (for example, `C:`) in Docker Desktop.

## 2) Environment Variables

Create a `.env` file at the project root with:

```env
# PostgreSQL
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=postgres_db

# Backend
POSTGRES_HOST=database
POSTGRES_PORT=5432

# App Ports
BACKEND_PORT=3003
FRONTEND_PORT=3004
```

Notes:
- `BACKEND_PORT` is the host machine port.
- Backend runs on container port 3000.
- `FRONTEND_PORT` is the host machine port.
- Frontend runs on container port 4000.

## 3) Run the Project

From the project root:

```bash
docker compose up -d --build
```

Check status:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f backend
docker compose logs -f frontend
docker compose logs -f database
```

## 4) Service URLs

- Frontend: `http://localhost:${FRONTEND_PORT}`
  - Default: `http://localhost:3004`
- Backend: `http://localhost:${BACKEND_PORT}`
  - Default: `http://localhost:3003`
- Database: `localhost:5432`

Backend test:

```bash
curl http://localhost:3003/
```

## 5) Development (Hot Reload)

Hot reload is already configured for Docker:
- Backend uses `nest start --watch`
- Frontend uses `next dev --webpack`
- Polling watchers are enabled for Docker/Windows environments

If changes are not reloaded, see Troubleshooting.

## 6) Common Commands

Stop services:

```bash
docker compose stop
```

Stop and remove containers/network:

```bash
docker compose down
```

Remove everything including volume (database data will be deleted):

```bash
docker compose down -v
```

Rebuild frontend/backend only:

```bash
docker compose up -d --build frontend backend
```

Open shell in containers:

```bash
docker compose exec backend sh
docker compose exec frontend sh
```

## 7) Troubleshooting

### 7.1 Hot reload is not working

Try in this order:
1. Check if containers are running.

```bash
docker compose ps
```

2. Check whether file changes are detected in logs.

```bash
docker compose logs -f backend
docker compose logs -f frontend
```

3. Recreate services with rebuild.

```bash
docker compose up -d --build backend frontend
```

4. On Windows, confirm Docker Desktop File Sharing is enabled for the project drive.

### 7.2 Cannot access frontend/backend

Check `.env` values:
- `BACKEND_PORT`
- `FRONTEND_PORT`

Then make sure those ports are not used by other applications.

### 7.3 Database fails to start

Check database logs:

```bash
docker compose logs -f database
```

Reset all data if needed:

```bash
docker compose down -v
docker compose up -d --build
```

## 8) Project Structure

```text
backend/     NestJS API
frontend/    Next.js app
docker-compose.yml
README.md
README.en.md
README.th.md
```

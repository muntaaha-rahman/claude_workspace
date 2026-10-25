---
name: fastapi-backend-conventions
description: Use before writing or editing FastAPI backend code — routers, services, SQLAlchemy models/schemas, auth, background jobs — in a FastAPI + async SQLAlchemy (asyncpg) + PostgreSQL + pydantic-settings stack. Covers reading the existing project layout and the conventions to match before writing anything.
---

# FastAPI backend conventions

The stack is consistently: FastAPI, async SQLAlchemy (asyncpg driver), Alembic, pydantic-settings, Redis, JWT auth via python-jose + passlib (bcrypt).

## Before writing anything

Read the existing project structure first and match it — don't impose a different layout. Look specifically at:
- `app/core/` for where settings, DB engine/session, security, and Redis client live. Naming varies by project — e.g. one project keeps the engine/session/Base in `config.py` and the `Settings` class in `settings.py`; don't assume the obvious split holds.
- How routers/services/schemas are organized (`app/modules/<name>/` vs `app/<name>/`).
- The existing async session dependency (usually `get_db` yielding an `AsyncSession`) — reuse it, don't invent a new one.

## Conventions to follow

- Everything touching the DB or Redis is `async def`; keep CPU-bound work (password hashing, JWT signing) sync.
- New enum columns: compare with `func.lower()` on both sides if the project's DB enums are uppercase while Python values are lowercase — check an existing enum column's usage first rather than assuming.
- Auth dependencies follow the existing `get_current_user` / `get_current_user_id` pattern — extend it, don't duplicate token-decoding logic elsewhere.
- Pull config from the `Settings` object (pydantic-settings) — never hardcode secrets, API keys, or environment-specific URLs in source. If a value needs an env var, add it to `Settings` with a sane non-secret default (or no default) and note it needs to be set in `.env`.
- Match existing error-handling style (`HTTPException` with the project's existing status-code/detail conventions) rather than introducing a new pattern.

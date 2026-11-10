# ERP Cloud

Cloud accounting & inventory SaaS for small/medium businesses in Jordan.
Multi-tenant NestJS + Prisma + MariaDB API, React + Ant Design web app.

**Full spec:** [`docs/BUILD_PLAN.md`](docs/BUILD_PLAN.md) · **Working rules:** [`CLAUDE.md`](CLAUDE.md)

## Prerequisites

- Node.js >= 20 (developed on Node 24)
- pnpm (enable via `corepack enable` — version pinned in `package.json`)
- MariaDB or MySQL 8 reachable via `DATABASE_URL`

## Quick start on Windows

Needs Node.js >= 22.18 and MariaDB/MySQL (e.g. XAMPP). Double-click **`start-local.bat`**. It:

1. checks Node.js and pnpm;
2. creates `.env` and `apps/api/.env` from the examples when they are missing;
3. checks the database server, and starts XAMPP's MariaDB (`C:\xampp` or `D:\xampp`) when it is stopped;
4. installs packages, creates the database, applies the migrations and the base seed;
5. starts the API (:3001) and the web app (:5173), and opens http://localhost:5173.

The first time it also loads a demo company: sign in with `demo@erp.local` / `Demo12345`. Run `start-local.bat -Demo` to load it into an existing database. Ctrl+C stops the API and the web app.

## Setup

```bash
corepack enable
pnpm install

cp .env.example .env            # fill in DATABASE_URL with real credentials
cp apps/api/.env.example apps/api/.env

# create the database, e.g.:
#   CREATE DATABASE erp_dev CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

pnpm --filter api prisma migrate dev --name init
pnpm dev                        # api on :3001, web on :5173
```

API docs (Swagger): http://localhost:3001/api/docs · Health: http://localhost:3001/api/v1/health

## Commands

```bash
pnpm dev                 # api + web in parallel
pnpm --filter api dev    # API only (nest start --watch)
pnpm --filter web dev    # web only (vite)
pnpm test                # all tests
pnpm typecheck
pnpm lint
pnpm api-client:generate # regenerate packages/api-client from OpenAPI (API must be running)
```

## Layout

```text
apps/api        NestJS modular monolith (see docs/BUILD_PLAN.md §4)
apps/web        React + Vite + Ant Design + TanStack Query + react-i18next
packages/api-client   orval-generated client (do not edit src/generated/)
docs/           BUILD_PLAN.md, ADRs, phase reports
```

## Conventions (summary — full rules in CLAUDE.md)

- Money/qty are never `number` — `Decimal`/string only.
- `tenant_id` comes from CLS context, never from client input.
- Journal entries only via `PostingService`; stock movements only via `StockLedgerService`.
- Posted documents are immutable; corrections via reversal.

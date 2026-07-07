# pipe23 conventions

The taste behind this template — what we reach for by default, and why. The mechanical bits are
*scaffolded* by the template; this doc records the *decisions*. Rule of thumb: **document the
taste, scaffold the boilerplate, brief the agent.**

Guiding principle: **overengineering is for people anticipating scale and reach — we anticipate
neither.** We build libraries, single images, and docker-compose compositions; not a SaaS. When a
category has a default, **pick one and ride it until it's obsolete** rather than re-litigating per
project.

## Stack defaults

| Concern | Default | Notes |
|---|---|---|
| Validation / models | **Pydantic v2** | `pydantic-settings` for config. msgpack only as a rare binary-wire exception to JSON. |
| Web framework | **FastAPI** | |
| API errors | **RFC 9457 problem+json** | Semantics per RFC 9110. Scaffolded handlers render HTTPException + validation errors. |
| ORM | **SQLAlchemy 2.0 (async)** | **Not SQLModel** — it bit us (under-maintained, awkward on non-trivial queries). psycopg3 also fine. |
| Migrations | **Alembic** | Used extensively. **Migrate-on-deploy via the entrypoint, no sidecar.** |
| Database | **Postgres** for real; **SQLite** for prototyping | Really open to SQLite. DuckDB fine for analytical/file work. Mongo only with a *very* compelling reason (rare). |
| Async DB driver | **asyncpg** (pg) / **aiosqlite** (sqlite) | |
| Config / secrets | **pydantic-settings: TOML + env** | TOML file for structure, env vars override scalars. **SOPS + age** is the intended upgrade for encrypted-at-rest secrets committed to git (Nathan reading up before adopting). |
| Scheduling / jobs | **APScheduler** | Go-to. Skip Celery's weight. |
| Cache / broker | **Redis** if genuinely needed | Rarely has been. (Dislike hand-writing its commands.) |
| Logging | **structlog** | Picked, riding. Events-with-fields (not format strings), JSON renderer for prod / pretty console for dev, stdlib bridge so uvicorn-et-al logs join the stream, log-capture testing. |
| ASGI server | **Granian** | Zero-config drop-in; asks as little of you as uvicorn did (nothing). |
| Routing / TLS | **Traefik** (label routing) | **mkcert** for local/PoC TLS; **ACME/Let's Encrypt** for a real domain. |
| TUI | **Textual / Rich** | The mock-wallet pattern. Pick one, ride it. |
| UI (web) | **HTMX** | Loosely held — recent, "generally don't care." Tailwind optional; **no Node toolchain imposed** (use the standalone Tailwind binary if needed). |
| Mobile | **React Native + native modules** | Expo is the modern default; willing to learn something else. |
| Deploy | **docker-compose only** | Single host, single images, or compositions. **No Kubernetes** — a scale tax we don't need. Docker + compose is grand. |
| Task runner | **Makefile** fine | `just` is the nicer modern option if ever wanted. |
| Packaging / quality | **uv + ruff + mypy(strict) + pytest** | **100% branch-coverage gate**, mkdocs-Material docs, PyPI **Trusted Publishing**. (All scaffolded by this template.) |

## Configuration

Every web service gets a `config.py` built on pydantic-settings. `load_config(path)` reads a TOML
file and returns a validated `Config`. Source priority, highest first: constructor arguments,
environment variables, the `.env` file, the TOML file, then model defaults. Environment variables
carry the prefix `MODULE_` and the nested delimiter `__`, so `MODULE_SECTION__KEY` overrides a
scalar within a section. Structure — lists and nested tables — comes from the TOML file; env vars
override scalars. `extra="forbid"`, so an unknown TOML key or an unknown prefixed env var is a
validation error. A missing, unparseable, or invalid file raises `ConfigError`. The `--config` path
is a runtime argument, which is why the module exposes a function rather than a module-level
singleton; database variants add a `settings` singleton for the import-time engine bootstrap.

## Compose hygiene (always)

`healthcheck:` on every service, `restart: unless-stopped`, `depends_on: condition:
service_healthy`, named volumes for stateful services, `env_file` optional so `compose config`
works before a `.env` exists. DB backups are **not** a current concern.

## Project lifecycle

Projects are born **private in the monorepo** (`workspace/projects/<name>`, fast iteration) and
**peeled to their own public repo** when ready (the pylongfellow pattern: fresh-history extract →
CI → branch protection → RTD → Trusted Publisher → tag). Public matters for the *projects* (free-
tier RTD + branch protection); tooling like this template can stay **private** (copier works fine
against a private repo over SSH).

## What's taste vs what's scaffolded

- **Scaffolded** (you get it for free, on-convention): uv/ruff/mypy/pytest/coverage, docs (incl.
  an OpenAPI page for services), CI, release pipeline, the FastAPI + structlog + problem+json +
  Dockerfile + compose + Alembic + migrate-on-deploy + Traefik/cert layer.
- **Taste** (your call per project, documented here so the default is known): which DB, HTMX vs
  not, whether you need Redis/jobs. The template offers the common shapes; this doc says which way
  to lean when unsure.

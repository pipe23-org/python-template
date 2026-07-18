# python-template

A [Copier](https://copier.readthedocs.io/) template for pipe23 Python projects, distilled out of
[`pipe23-org/pylongfellow`](https://github.com/pipe23-org/pylongfellow) after its peel-out.
Generated projects build, lint, type-check, test, and document out of the box:

- **uv** — packaging, locking, and task running; no requirements.txt
- **ruff** (lint + format) and **mypy** in strict mode
- **pytest** with a 100% branch-coverage gate
- **mkdocs-Material** docs with a Read the Docs config
- **GitHub Actions CI** — lint / types / docs / test matrix (`python_min` through 3.14)
- **Release pipeline** to PyPI via Trusted Publishing (no tokens)
- **Dependabot** for actions and dependencies
- A generated **SETUP.md** — the go-public checklist (branch protection, RTD import, Trusted
  Publisher registration), with the traps we hit baked in

## Options

Answers to the Copier prompts select overlays:

- `is_application` — console-script entry point; `cli_lib` chooses argparse (default) or click.
  Library vs application is not a fork — an application is a package with a `[project.scripts]`
  entry.
- `publish_to_pypi` — include the Trusted-Publishing release workflow
- `native_extension` — C/C++ overlay: CMake + cffi build, cibuildwheel wheel matrix
- `web_service` — FastAPI service: structlog logging, RFC 9457 problem+json error handling,
  TOML + env config via pydantic-settings, OpenAPI page in the docs site, multi-stage
  Dockerfile, compose file with Traefik routing and mkcert/ACME TLS, GHCR image job
- `database` (web services) — SQLite or PostgreSQL: SQLAlchemy 2.0 async, Alembic migrations
  applied on deploy by the entrypoint

## Repo layout

- `copier.yml` — the questions, defaults, and Jinja delimiter config
- `template/` — the tree that gets rendered into a new project
- `.github/workflows/smoke.yml` — CI for the template itself: generates a project per answer
  combo and runs the generated project's own gates

File and directory names under `template/` like `[% if web_service %]deploy[% endif %]` are
Copier conditional paths: the segment renders only when the condition holds, so a non-web-service
project contains no `deploy/` at all. This template uses `[% %]` / `[[ ]]` as Jinja delimiters
instead of the defaults so GitHub Actions' own `${{ }}` expressions pass through untouched.

## Use it

```bash
# new project
uvx copier copy https://github.com/pipe23-org/python-template ../my-new-thing

# later, pull template improvements into a generated project
cd ../my-new-thing && uvx copier update
```

Copier copies from the latest tag; tags are the template's release channel. After generating,
run `uv lock`, then work through the generated `SETUP.md`.

## License

Apache-2.0. The license covers this template's own files; projects you generate from it are
yours, with no obligations back to this repo.

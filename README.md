# python-template

A [Copier](https://copier.readthedocs.io/) template for pipe23 Python projects — the scaffolding
distilled out of `pipe23-org/pylongfellow` after its peel-out: uv + ruff + mypy + pytest, an
mkdocs-Material docs site, CI, and a Trusted-Publishing release pipeline. Pure-Python by default,
with an optional **native (CMake + cffi + cibuildwheel)** overlay.

> Library vs application is *not* a fork here — an application is just a package with a
> `[project.scripts]` entry point (answer `is_application: yes`). The real fork is pure-Python vs
> native extension.

## Use it

```bash
# new project (private repo — copier reads it over SSH)
uvx copier copy git@github.com:pipe23-org/python-template.git ../my-new-thing

# later, pull template improvements into an already-generated project
cd ../my-new-thing && uvx copier update
```

Answer the prompts (project name, description, Python floor, whether it's an app, whether to
publish to PyPI, whether it's a native extension) and you get a repo that builds, lints, types,
tests, and docs out of the box.

## After generating

Follow **`SETUP.md`** in the generated project — it's the go-public checklist (Trusted Publisher,
RTD import, branch protection) with the gotchas we actually hit baked in.

## As-yet untested

The `web_service` overlay additions (structlog config, RFC 9457 problem handlers, OpenAPI page in
the docs site) have been verified only by generating projects locally and running their full gate
suites. Never yet exercised: a real GitHub Actions run of the generated CI, a real Read the Docs
build (which for web services now installs the full app environment to import the app for
`app.openapi()`), and the swagger page on a deployed docs site. First real consumer finds out;
deal as we go.

## What's a placeholder

`project_name`, `module_name`, `description`, `author_*`, `github_org`, `repo_name`, `python_min`,
`is_application` / `cli_command`, `publish_to_pypi`, `native_extension`. See `copier.yml`.

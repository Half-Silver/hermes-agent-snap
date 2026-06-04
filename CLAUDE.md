# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is **`hermes-agent-snap`** — a fork of [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) (tracked as the `upstream` remote) whose added purpose is to **package Hermes Agent as a strictly-confined Snap (`all-dev-hermesagent`)** for Ubuntu Core devices, integrated with the ALL **Control Tower** via a sidecar `ct-engine`.

So there are two hats here:
- **Upstream Hermes development** — the Python agent itself (`run_agent.py`, `cli.py`, `tools/`, `gateway/`, …). Periodically merged from `upstream/main`.
- **Snap/deployment packaging** — `snap/`, `plugin.yaml`, and the `SNAP-CONFIG.md` / `CONTROL-TOWER.md` / `NUC-DEPLOYMENT-LOG.md` docs. This is the fork's reason to exist and is **not** in upstream.

Note `pyproject.toml` is at `0.13.0` (upstream) while the snap (`snap/snapcraft.yaml`, `plugin.yaml`) is pinned to `0.12.0` — they version independently. GitHub workflows are disabled in this fork (see `.github/workflows-disabled/`); CI does not run here, so run checks locally.

## Read AGENTS.md first

`AGENTS.md` (~45KB, kept current) is the canonical, detailed development guide for the *agent codebase*: AIAgent loop, CLI/TUI architecture, adding tools/commands/config, toolsets, plugins, skills, delegation, curator, cron, kanban, profiles, and a long list of known pitfalls. **Do not duplicate it here — read it.** This file covers only the essential commands, the few rules you can't afford to miss, and the snap-packaging layer that AGENTS.md doesn't mention.

## Commands

```bash
# Setup (one-shot): installs uv, makes .venv, installs .[all], symlinks ~/.local/bin/hermes
./setup-hermes.sh
# Manual equivalent:
uv venv .venv --python 3.11 && source .venv/bin/activate && uv pip install -e ".[all,dev]"

# Tests — ALWAYS via the wrapper, NEVER raw `pytest` (see below)
scripts/run_tests.sh                                   # full suite, CI-parity
scripts/run_tests.sh tests/gateway/                    # one directory
scripts/run_tests.sh tests/agent/test_foo.py::test_x   # one test
scripts/run_tests.sh -v --tb=long                      # pass-through pytest flags

# Lint / typecheck (workflows are disabled, so run these yourself)
ruff check .        # only PLW1514 (unspecified-encoding) is enabled — see [tool.ruff.lint] in pyproject.toml
ty check            # type checker (astral `ty`), config under [tool.ty.*]

# Run the agent
hermes              # interactive CLI (prompt_toolkit)
hermes --tui        # Ink/React TUI (or HERMES_TUI=1)
hermes gateway start
hermes doctor       # diagnostics

# TUI dev (ui-tui/ — Ink/React)
cd ui-tui && npm install && npm run dev    # also: build, type-check, lint, fmt, test

# Web dashboard SPA (web/ — built into hermes_cli/web_dist at package time)
cd web && npm install && npm run dev
```

### Test runner is mandatory — do not call `pytest` directly

`scripts/run_tests.sh` enforces hermetic parity with CI: it unsets all credential-shaped env vars (`*_API_KEY`, `*_TOKEN`, …) and `HERMES_*` behavioral vars, pins `TZ=UTC` / `LANG=C.UTF-8` / `PYTHONHASHSEED=0`, and uses **4 xdist workers** (not `-n auto`). Raw `pytest` on a many-core dev machine with API keys set diverges from CI in ways that have repeatedly caused "works locally, fails in CI" (and the reverse). `tests/conftest.py` also redirects `HERMES_HOME` to a temp dir per test — never hardcode `~/.hermes/` in tests. Run the full suite before pushing.

## Architecture (big picture)

Python 3.11+. Entry points (`pyproject.toml [project.scripts]`): `hermes` → `hermes_cli.main:main`, `hermes-agent` → `run_agent:main`, `hermes-acp` → `acp_adapter.entry:main`.

Import/dependency chain (load-bearing):
```
tools/registry.py            # no deps; imported by every tool file
  ↑ tools/*.py               # each self-registers via registry.register() at import
  ↑ model_tools.py           # triggers tool discovery + plugin discovery; handle_function_call()
  ↑ run_agent.py / cli.py / batch_runner.py
```

- `run_agent.py` — `AIAgent`, the synchronous conversation loop (`run_conversation()`). Messages use OpenAI chat format.
- `model_tools.py` — tool orchestration and dispatch. Importing it is also what runs `discover_plugins()`.
- `toolsets.py` — `TOOLSETS` dict + `_HERMES_CORE_TOOLS`. A registered tool is only *exposed to an agent* if its name appears in a toolset.
- `cli.py` — `HermesCLI` interactive orchestrator; `hermes_cli/commands.py` is the single `COMMAND_REGISTRY` that every surface (CLI, gateway, Telegram, Slack, autocomplete, help) derives from.
- `gateway/` — single multi-platform messaging process (`run.py` + `platforms/`).
- `ui-tui/` (Ink/React) ↔ `tui_gateway/` (Python JSON-RPC over stdio) — the TUI; the `hermes dashboard` web chat embeds this same TUI over a PTY, it is not a rewrite.

## Rules you can't afford to miss

These are the highest-consequence items from AGENTS.md — read it for the rest.

- **Profile-safe paths.** Never hardcode `~/.hermes` or `Path.home() / ".hermes"`. Use `get_hermes_home()` (code paths) and `display_hermes_home()` (user-facing strings) from `hermes_constants`. Hardcoding breaks the multi-profile / multi-`HERMES_HOME` model. In the snap, `HERMES_HOME` lives under `/var/snap/all-dev-hermesagent/common/`, so this matters in production.
- **Don't break prompt caching.** Never alter past context, change toolsets, or rebuild the system prompt mid-conversation. Cache-mutating slash commands default to deferred invalidation with an opt-in `--now`.
- **Plugins must not modify core files** (`run_agent.py`, `cli.py`, `gateway/run.py`, `hermes_cli/main.py`). Extend the generic plugin surface instead. New custom tools go through the plugin route (`~/.hermes/plugins/`), not by editing `tools/`/`toolsets.py` — the built-in route is only for tools genuinely contributed to core.
- **All tool handlers return a JSON string.**
- When merging from `upstream`, beware stale squash-merges silently reverting fixes, and re-verify the snap layer still builds (the upstream merge won't touch `snap/`, but version/structure drift can).

## Snap packaging & Control Tower (this fork's layer)

The snap is named **`all-dev-hermesagent`**, `confinement: strict`, `base: core24`. Built with snapcraft from `snap/snapcraft.yaml`.

Parts: `web-frontend` (builds `web/` → `hermes_cli/web_dist`), `hermes` (Python `.[all]` + staged `git`/`ripgrep`/`ffmpeg`/`curl`), `browser-tools` (npm `agent-browser` + Playwright/Camoufox libs), `all-snap-plugin` (the `ct-engine` binary, sourced from a sibling `../all_snap_plugin` at build time — not in this repo), and `wrappers` (`snap/local/hermes-wrapper`, `plugin.yaml`).

Snap apps/daemons (all `install-mode: disable` except the `hermes` CLI): `agent`, `acp`, `dashboard` (web UI on **port 9119**), `gateway`, and `ct-engine` (Control Tower sidecar). Hooks: `snap/hooks/configure`, `snap/hooks/install`.

**Configuration is done with `snap set all-dev-hermesagent <key>=<value>`** — the `configure` hook routes each key to either `/var/snap/all-dev-hermesagent/common/.env` (API keys/tokens) or `.../config.yaml` (settings). The key→destination mapping is documented in `SNAP-CONFIG.md`. Service toggles: `dashboard-service` / `gateway-service` / `agent-service` / `acp-service` = `enabled`|`disabled`.

`plugin.yaml` is the Control Tower manifest consumed by `ct-engine` in **sidecar mode**: it declares the configurable keys (provider keys, model, service toggles, log-level, personality) and reports the dashboard URL back to Control Tower via `ct-callback-url` / `ct-node-id` / `ct-deployment-id`. See `CONTROL-TOWER.md` for the integration flow and `NUC-DEPLOYMENT-LOG.md` for the exact deploy/connect/verify command sequence used on the target device (Basil-nuc).

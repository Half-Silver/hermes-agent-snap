# Hermes Snap — Configuration Reference

All configuration is done through `snap set all-dev-hermesagent <key>=<value>`.
The configure hook routes each key to the right place automatically:
- **API keys & tokens** → `/var/snap/all-dev-hermesagent/common/.env`
- **Settings** → `/var/snap/all-dev-hermesagent/common/config.yaml`

## Quick Start

```bash
# Install
sudo snap install all-dev-hermesagent_0.12.0_amd64.snap --dangerous

# Set your API key (pick one provider)
sudo snap set all-dev-hermesagent openrouter-api-key=sk-or-your-key-here

# Set the model
sudo snap set all-dev-hermesagent model=openrouter/deepseek/deepseek-r1

# Dashboard is auto-enabled — open in browser
# http://<device-ip>:9119
```

---

## LLM Provider API Keys

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set all-dev-hermesagent openai-api-key=sk-...` | `OPENAI_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent openai-base-url=https://...` | `OPENAI_BASE_URL` | `.env` |
| `sudo snap set all-dev-hermesagent openrouter-api-key=sk-or-...` | `OPENROUTER_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent anthropic-api-key=sk-ant-...` | `ANTHROPIC_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent gemini-api-key=...` | `GEMINI_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent deepseek-api-key=...` | `DEEPSEEK_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent xai-api-key=...` | `XAI_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent ollama-base-url=http://...` | `OLLAMA_BASE_URL` | `.env` |

## Tool API Keys

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set all-dev-hermesagent exa-api-key=...` | `EXA_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent tavily-api-key=...` | `TAVILY_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent firecrawl-api-key=...` | `FIRECRAWL_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent browserbase-api-key=...` | `BROWSERBASE_API_KEY` | `.env` |
| `sudo snap set all-dev-hermesagent browserbase-project-id=...` | `BROWSERBASE_PROJECT_ID` | `.env` |

## Messaging Platform Tokens

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set all-dev-hermesagent telegram-bot-token=...` | `TELEGRAM_BOT_TOKEN` | `.env` |
| `sudo snap set all-dev-hermesagent telegram-allowed-users=...` | `TELEGRAM_ALLOWED_USERS` | `.env` |
| `sudo snap set all-dev-hermesagent discord-bot-token=...` | `DISCORD_BOT_TOKEN` | `.env` |
| `sudo snap set all-dev-hermesagent discord-allowed-users=...` | `DISCORD_ALLOWED_USERS` | `.env` |
| `sudo snap set all-dev-hermesagent slack-bot-token=...` | `SLACK_BOT_TOKEN` | `.env` |
| `sudo snap set all-dev-hermesagent slack-app-token=...` | `SLACK_APP_TOKEN` | `.env` |

## Model & Settings

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set all-dev-hermesagent model=openrouter/deepseek/deepseek-r1` | `model` | `config.yaml` |
| `sudo snap set all-dev-hermesagent model-provider=openrouter` | `model.provider` | `config.yaml` |
| `sudo snap set all-dev-hermesagent model-name=deepseek-r1` | `model.name` | `config.yaml` |
| `sudo snap set all-dev-hermesagent personality="You are a helpful assistant"` | `personality` | `config.yaml` |
| `sudo snap set all-dev-hermesagent log-level=debug` | `logging.level` | `config.yaml` |

## Service Control

| `snap set` command | Effect |
|---|---|
| `sudo snap set all-dev-hermesagent dashboard-service=enabled` | Start the web dashboard (default: **enabled**) |
| `sudo snap set all-dev-hermesagent dashboard-service=disabled` | Stop the web dashboard |
| `sudo snap set all-dev-hermesagent gateway-service=auto` | **Default.** Gateway runs only when a messaging token is configured; comes up automatically when you add one (web UI or `snap set`), stops when none is set |
| `sudo snap set all-dev-hermesagent gateway-service=enabled` | Pin the messaging gateway on (overrides `auto`) |
| `sudo snap set all-dev-hermesagent gateway-service=disabled` | Pin the messaging gateway off (overrides `auto`) |
| `sudo snap set all-dev-hermesagent agent-service=enabled` | Start the AI agent daemon |
| `sudo snap set all-dev-hermesagent agent-service=disabled` | Stop the AI agent daemon |
| `sudo snap set all-dev-hermesagent acp-service=enabled` | Start the ACP adapter |
| `sudo snap set all-dev-hermesagent acp-service=disabled` | Stop the ACP adapter |
| `sudo snap set all-dev-hermesagent config-watcher-service=enabled` | Apply web-UI config changes automatically (default: **enabled**) |
| `sudo snap set all-dev-hermesagent config-watcher-service=disabled` | Turn off auto-apply; config then applies only on `snap set` |

### How config changes apply (headless / web-UI driven)

The dashboard writes API keys and tokens to `.env` / `config.yaml`, but a web
form cannot restart the snap's daemons. The **config-watcher** service closes
that gap: it watches the messaging-token slice of `.env` and, when it changes,
reconciles the `gateway` service via `snapctl` so the new token connects — no
SSH or `snap set` required.

- **LLM / provider keys** (OpenRouter, OpenAI, …) need no restart — a running
  gateway hot-reloads `.env` credentials on every turn.
- **Messaging tokens** (Telegram/Discord/Slack) bring the gateway up (or, when
  removed, take it down) on the next watcher tick (a few seconds).
- Setting a key via `snap set` runs the same reconcile through the configure
  hook, so both paths stay consistent.

Watch it work: `snap logs all-dev-hermesagent.config-watcher -f`

## CT Engine Integration

These are set automatically by the Control Tower during deployment:

| `snap set` command | Description |
|---|---|
| `sudo snap set all-dev-hermesagent ct-callback-url=http://...` | CT callback endpoint (enables ct-engine) |
| `sudo snap set all-dev-hermesagent ct-snap-name=all-dev-hermesagent` | Snap identifier for CT |
| `sudo snap set all-dev-hermesagent ct-node-id=node-001` | Node identifier |
| `sudo snap set all-dev-hermesagent ct-deployment-id=deploy-001` | Deployment identifier |

---

## File Locations

| Path | Contents |
|---|---|
| `/var/snap/all-dev-hermesagent/common/.env` | API keys and secrets |
| `/var/snap/all-dev-hermesagent/common/config.yaml` | Model, logging, personality settings |
| `/var/snap/all-dev-hermesagent/common/` | Full `HERMES_HOME` directory |
| `/snap/all-dev-hermesagent/current/` | Read-only snap installation |

## Services

| Service | Command |
|---|---|
| `snap logs all-dev-hermesagent.dashboard` | Dashboard logs |
| `snap logs all-dev-hermesagent.gateway` | Messaging gateway logs |
| `snap logs all-dev-hermesagent.agent` | Agent daemon logs |
| `snap logs all-dev-hermesagent.acp` | ACP adapter logs |
| `snap logs all-dev-hermesagent.ct-engine` | CT Engine sidecar logs |
| `snap logs all-dev-hermesagent.config-watcher` | Config-watcher (auto-apply) logs |

## Example: Full Setup

```bash
# 1. Install the snap
sudo snap install all-dev-hermesagent_0.12.0_amd64.snap --dangerous

# 2. Set API key
sudo snap set all-dev-hermesagent openrouter-api-key=sk-or-v1-abc123

# 3. Set model
sudo snap set all-dev-hermesagent model=openrouter/deepseek/deepseek-r1

# 4. Enable CT Engine (done automatically by Control Tower)
sudo snap set all-dev-hermesagent ct-callback-url=http://192.168.1.6:8080/callback

# 5. Dashboard is at http://<device-ip>:9119
# All further configuration is done through the dashboard UI
```

# Hermes Snap — Configuration Reference

All configuration is done through `snap set hermes <key>=<value>`.
The configure hook routes each key to the right place automatically:
- **API keys & tokens** → `/var/snap/hermes/common/.env`
- **Settings** → `/var/snap/hermes/common/config.yaml`

## Quick Start

```bash
# Install
sudo snap install hermes_0.12.0_amd64.snap --dangerous

# Set your API key (pick one provider)
sudo snap set hermes openrouter-api-key=sk-or-your-key-here

# Set the model
sudo snap set hermes model=openrouter/deepseek/deepseek-r1

# Dashboard is auto-enabled — open in browser
# http://<device-ip>:9119
```

---

## LLM Provider API Keys

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set hermes openai-api-key=sk-...` | `OPENAI_API_KEY` | `.env` |
| `sudo snap set hermes openai-base-url=https://...` | `OPENAI_BASE_URL` | `.env` |
| `sudo snap set hermes openrouter-api-key=sk-or-...` | `OPENROUTER_API_KEY` | `.env` |
| `sudo snap set hermes anthropic-api-key=sk-ant-...` | `ANTHROPIC_API_KEY` | `.env` |
| `sudo snap set hermes gemini-api-key=...` | `GEMINI_API_KEY` | `.env` |
| `sudo snap set hermes deepseek-api-key=...` | `DEEPSEEK_API_KEY` | `.env` |
| `sudo snap set hermes xai-api-key=...` | `XAI_API_KEY` | `.env` |
| `sudo snap set hermes ollama-base-url=http://...` | `OLLAMA_BASE_URL` | `.env` |

## Tool API Keys

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set hermes exa-api-key=...` | `EXA_API_KEY` | `.env` |
| `sudo snap set hermes tavily-api-key=...` | `TAVILY_API_KEY` | `.env` |
| `sudo snap set hermes firecrawl-api-key=...` | `FIRECRAWL_API_KEY` | `.env` |
| `sudo snap set hermes browserbase-api-key=...` | `BROWSERBASE_API_KEY` | `.env` |
| `sudo snap set hermes browserbase-project-id=...` | `BROWSERBASE_PROJECT_ID` | `.env` |

## Messaging Platform Tokens

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set hermes telegram-bot-token=...` | `TELEGRAM_BOT_TOKEN` | `.env` |
| `sudo snap set hermes telegram-allowed-users=...` | `TELEGRAM_ALLOWED_USERS` | `.env` |
| `sudo snap set hermes discord-bot-token=...` | `DISCORD_BOT_TOKEN` | `.env` |
| `sudo snap set hermes discord-allowed-users=...` | `DISCORD_ALLOWED_USERS` | `.env` |
| `sudo snap set hermes slack-bot-token=...` | `SLACK_BOT_TOKEN` | `.env` |
| `sudo snap set hermes slack-app-token=...` | `SLACK_APP_TOKEN` | `.env` |

## Model & Settings

| `snap set` command | Hermes config key | File |
|---|---|---|
| `sudo snap set hermes model=openrouter/deepseek/deepseek-r1` | `model` | `config.yaml` |
| `sudo snap set hermes model-provider=openrouter` | `model.provider` | `config.yaml` |
| `sudo snap set hermes model-name=deepseek-r1` | `model.name` | `config.yaml` |
| `sudo snap set hermes personality="You are a helpful assistant"` | `personality` | `config.yaml` |
| `sudo snap set hermes log-level=debug` | `logging.level` | `config.yaml` |

## Service Control

| `snap set` command | Effect |
|---|---|
| `sudo snap set hermes dashboard-service=enabled` | Start the web dashboard (default: **enabled**) |
| `sudo snap set hermes dashboard-service=disabled` | Stop the web dashboard |
| `sudo snap set hermes gateway-service=enabled` | Start the messaging gateway (Telegram/Discord/Slack) |
| `sudo snap set hermes gateway-service=disabled` | Stop the messaging gateway |
| `sudo snap set hermes agent-service=enabled` | Start the AI agent daemon |
| `sudo snap set hermes agent-service=disabled` | Stop the AI agent daemon |
| `sudo snap set hermes acp-service=enabled` | Start the ACP adapter |
| `sudo snap set hermes acp-service=disabled` | Stop the ACP adapter |

## CT Engine Integration

These are set automatically by the Control Tower during deployment:

| `snap set` command | Description |
|---|---|
| `sudo snap set hermes ct-callback-url=http://...` | CT callback endpoint (enables ct-engine) |
| `sudo snap set hermes ct-snap-name=hermes` | Snap identifier for CT |
| `sudo snap set hermes ct-node-id=node-001` | Node identifier |
| `sudo snap set hermes ct-deployment-id=deploy-001` | Deployment identifier |

---

## File Locations

| Path | Contents |
|---|---|
| `/var/snap/hermes/common/.env` | API keys and secrets |
| `/var/snap/hermes/common/config.yaml` | Model, logging, personality settings |
| `/var/snap/hermes/common/` | Full `HERMES_HOME` directory |
| `/snap/hermes/current/` | Read-only snap installation |

## Services

| Service | Command |
|---|---|
| `snap logs hermes.dashboard` | Dashboard logs |
| `snap logs hermes.gateway` | Messaging gateway logs |
| `snap logs hermes.agent` | Agent daemon logs |
| `snap logs hermes.acp` | ACP adapter logs |
| `snap logs hermes.ct-engine` | CT Engine sidecar logs |

## Example: Full Setup

```bash
# 1. Install the snap
sudo snap install hermes_0.12.0_amd64.snap --dangerous

# 2. Set API key
sudo snap set hermes openrouter-api-key=sk-or-v1-abc123

# 3. Set model
sudo snap set hermes model=openrouter/deepseek/deepseek-r1

# 4. Enable CT Engine (done automatically by Control Tower)
sudo snap set hermes ct-callback-url=http://192.168.1.6:8080/callback

# 5. Dashboard is at http://<device-ip>:9119
# All further configuration is done through the dashboard UI
```

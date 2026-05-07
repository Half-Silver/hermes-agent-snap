# Control Tower Integration — all-dev-hermesagent

This document defines the input schema and configuration required for the **ALL Control Tower** to manage this snap.

## 📄 Snap Manifest (JSON)

Use this JSON when registering the snap in the Control Tower dashboard.

```json
{
  "name": "all-dev-hermesagent",
  "version": "0.12.0",
  "type": "sidecar",
  "config": {
    "model": "gpt-4o",
    "log-level": "info",
    "openai-api-key": "",
    "openrouter-api-key": "",
    "telegram-bot-token": "",
    "telegram-allowed-users": "",
    "ct-callback-url": "http://<ct-ip>:8080/callback"
  },
  "output": {
    "mode": "logs",
    "interval": 0,
    "initial_event": "message_initial",
    "stop_event": "deployment_stop"
  }
}
```

## 🛠️ Input Schema (Configuration)

| Key | Type | Description |
|-----|------|-------------|
| `model` | `string` | Primary AI model (e.g., `gpt-4o`, `openrouter/deepseek/deepseek-r1`) |
| `openai-api-key` | `string` | OpenAI API Key (starts with `sk-`) |
| `openrouter-api-key` | `string` | OpenRouter API Key (starts with `sk-or-v1-`) |
| `telegram-bot-token` | `string` | Telegram Bot Token from @BotFather |
| `telegram-allowed-users` | `string` | Comma-separated list of Telegram User IDs |
| `ct-callback-url` | `string` | The Control Tower callback URL for status reporting |

## 📡 Status Reporting (Sidecar)

Hermes reports its status via periodic callbacks to the `ct-callback-url`. 

### Initial Message
Upon startup, Hermes sends a `message_initial` event containing:
- Dashboard URL (`http://<ip>:9119`)
- List of enabled/disabled services.
- Masked API key verification.

### Shutdown
Upon stopping, Hermes sends a `deployment_stop` event to notify the Control Tower of graceful closure.

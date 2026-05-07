# Control Tower Integration — all-dev-hermesagent

This snap uses the **ALL Universal Snap Engine (`ct-engine`)** for seamless Control Tower integration. All app-specific behavior is defined in the `plugin.yaml` manifest.

## 🏗️ Architecture

```text
all-dev-hermesagent:
├── ct-engine          ← universal integration engine
├── plugin.yaml        ← Hermes-specific manifest (defines the UI & config)
└── hermes (binary)    ← the actual AI agent
```

## 📄 The Manifest (`plugin.yaml`)

This is the core definition used by the `ct-engine` to bridge the snap to the Control Tower.

```yaml
app:
  name: "all-dev-hermesagent"
  version: "0.12.0"

config:
  # --- Provider Keys ---
  openrouter-api-key: { required: false, type: "string" }
  openai-api-key: { required: false, type: "string" }
  anthropic-api-key: { required: false, type: "string" }
  
  # --- Settings ---
  model: { required: false, type: "string", default: "gpt-4o" }
  log-level: { required: false, type: "string", default: "info" }

output:
  mode: "logs"
  interval: 0  # Sidecar mode (reports status on startup)
  initial_event: "message_initial"
```

## 📡 How it works

1. **Deployment**: Control Tower sends a `snap set` command to the node.
2. **Configuration**: The snap's `configure` hook updates the internal `.env`.
3. **Execution**: The `ct-engine` starts, reads `plugin.yaml`, and loads the current config via `snapctl`.
4. **Reporting**: The engine sends a `message_initial` callback to the Control Tower with the Dashboard URL (`http://<ip>:9119`).

## 🛠️ Control Tower JSON Reference

When registering this snap in the Control Tower, use this configuration:

```json
{
  "name": "all-dev-hermesagent",
  "type": "sidecar",
  "config": {
    "model": "gpt-4o",
    "openrouter-api-key": "sk-or-v1-...",
    "ct-callback-url": "http://<ct-ip>:8080/callback"
  }
}
```

# Hermes (ALL Edition) — Setup Guide

This guide will walk you through installing and configuring **all-dev-hermesagent** on your device.

## 1. Installation

Install the snap from your local build:
```bash
sudo snap install all-dev-hermesagent_0.12.0_amd64.snap --dangerous
```

### Connect Plugs
Ensure the snap has access to your system files:
```bash
sudo snap connect all-dev-hermesagent:home :home
sudo snap connect all-dev-hermesagent:dot-hermes :personal-files
```

## 2. Dashboard Access

Hermes provides a Web UI for monitoring and agent management.

1. Open your browser to `http://<your-device-ip>:9119`.
2. All configuration changes in Step 4 will be reflected here automatically.

## 3. Control Tower Integration (Optional)

If you are using the ALL Control Tower, you can automate status reporting:

```bash
# Set your CT Callback URL
sudo snap set all-dev-hermesagent ct-callback-url=http://<ct-ip>:8080/callback

# (Optional) Set Deployment IDs
sudo snap set all-dev-hermesagent ct-deployment-id=deploy-001
sudo snap set all-dev-hermesagent ct-node-id=node-001
```

Once connected, Hermes will automatically report the dashboard link to your Control Tower.

## 4. Configuration (API Keys)

You can configure your AI providers and messaging channels directly from the terminal.

### Primary Settings
```bash
# Set your primary model
sudo snap set all-dev-hermesagent model=openrouter/deepseek/deepseek-r1
```

### AI Providers
```bash
sudo snap set all-dev-hermesagent openrouter-api-key=sk-or-v1-...
sudo snap set all-dev-hermesagent openai-api-key=sk-...
sudo snap set all-dev-hermesagent anthropic-api-key=sk-ant-...
sudo snap set all-dev-hermesagent gemini-api-key=...
```

### Tools & Search
```bash
sudo snap set all-dev-hermesagent exa-api-key=...
sudo snap set all-dev-hermesagent tavily-api-key=...
sudo snap set all-dev-hermesagent firecrawl-api-key=...
```

## 5. Service Management

You can enable or disable parts of the system:

```bash
# Disable the Telegram/Discord gateway
sudo snap set all-dev-hermesagent gateway-service=disabled

# Enable the dashboard (enabled by default)
sudo snap set all-dev-hermesagent dashboard-service=enabled
```

---

## Troubleshooting

### View Logs
```bash
sudo snap logs all-dev-hermesagent -f
```

### Check Status
```bash
snap services all-dev-hermesagent
```

### Reset Permissions
If the `.env` file becomes unreadable:
```bash
sudo chmod 644 /var/snap/all-dev-hermesagent/common/.env
```

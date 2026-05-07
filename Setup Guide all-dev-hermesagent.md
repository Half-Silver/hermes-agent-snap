# Hermes (ALL Edition) — Setup Guide

This guide will walk you through installing and configuring **all-dev-hermesagent** on your device.

## 🚀 Full One-Line Setup
If you want to get everything running in one go:
```bash
sudo snap connect all-dev-hermesagent:home :home && \
sudo snap connect all-dev-hermesagent:removable-media :removable-media && \
sudo snap connect all-dev-hermesagent:ssh-keys :ssh-keys && \
sudo snap connect all-dev-hermesagent:dot-hermes :personal-files && \
sudo snap set all-dev-hermesagent acp-service=enabled && \
sudo snap set all-dev-hermesagent agent-service=enabled && \
sudo snap set all-dev-hermesagent gateway-service=enabled && \
sudo snap set all-dev-hermesagent dashboard-service=enabled && \
sudo snap set all-dev-hermesagent ct-callback-url=http://<ct-ip>:8080/callback && \
sudo snap set all-dev-hermesagent ct-deployment-id=deploy-001 && \
sudo snap set all-dev-hermesagent ct-node-id=node-001 && \
sudo snap set all-dev-hermesagent ct-snap-name=all-dev-hermesagent
```

### AI Providers & Models
```bash
# Set Model Provider and Name
sudo snap set all-dev-hermesagent model-provider=openrouter
sudo snap set all-dev-hermesagent model-name=minimax/minimax-m2.5:free

# Set API Keys
sudo snap set all-dev-hermesagent openrouter-api-key=sk-or-v1-...
sudo snap set all-dev-hermesagent openai-api-key=sk-...
```

### Messaging Channels
```bash
# Set Bot Token
sudo snap set all-dev-hermesagent telegram-bot-token=8749...bxIk

# Set Multi-User Allowlist (comma-separated)
sudo snap set all-dev-hermesagent telegram-allowed-users=8295487214,18813613
```

---

## 1. Installation & Interfaces

Install the snap from your local build:
```bash
sudo snap install all-dev-hermesagent_0.12.0_amd64.snap --dangerous
```

### Connect Interfaces
```bash
sudo snap connect all-dev-hermesagent:home :home
sudo snap connect all-dev-hermesagent:removable-media :removable-media
sudo snap connect all-dev-hermesagent:ssh-keys :ssh-keys
sudo snap connect all-dev-hermesagent:dot-hermes :personal-files
```

## 2. Dashboard Access
Hermes provides a Web UI for monitoring and agent management.
* **URL**: `http://<device-ip>:9119`
* **Check Port**: `ss -tulnp | grep 9119`

## 3. Service Management

### Enable/Disable Services
```bash
# Enable
sudo snap set all-dev-hermesagent acp-service=enabled agent-service=enabled gateway-service=enabled dashboard-service=enabled

# Disable
sudo snap set all-dev-hermesagent acp-service=disabled agent-service=disabled gateway-service=disabled dashboard-service=disabled
```

### Restart Services
```bash
# Restart Everything
sudo snap restart all-dev-hermesagent

# Individual Services
sudo snap restart all-dev-hermesagent.acp
sudo snap restart all-dev-hermesagent.agent
sudo snap restart all-dev-hermesagent.gateway
sudo snap restart all-dev-hermesagent.dashboard
sudo snap restart all-dev-hermesagent.ct-engine
```

## 4. Configuration (API Keys & Model)

### AI Settings
```bash
# Set Model
sudo snap set all-dev-hermesagent model=gpt-4o

# Set API Keys
sudo snap set all-dev-hermesagent openai-api-key=sk-...
sudo snap set all-dev-hermesagent openrouter-api-key=sk-or-v1-...
```

### System Settings
```bash
sudo snap set all-dev-hermesagent log-level=info
```

## 5. Monitoring & Debugging

### Follow Live Logs
```bash
# All Services
snap logs all-dev-hermesagent -f

# Specific Service (Last 100 lines)
snap logs all-dev-hermesagent.dashboard -n 100 -f
```

### Systemd Logs
```bash
journalctl -u snap.all-dev-hermesagent.acp.service -n 100 --no-pager
```

### Check Interfaces
```bash
snap connections all-dev-hermesagent
snap interface personal-files
```

---

## 📂 File Locations

| Path Type | Location |
|-----------|----------|
| **Shared Data** | `/var/snap/all-dev-hermesagent/common/` |
| **Secrets (.env)** | `/var/snap/all-dev-hermesagent/common/.env` |
| **Config (YAML)** | `/var/snap/all-dev-hermesagent/common/config.yaml` |
| **User Settings** | `~/snap/all-dev-hermesagent/` |

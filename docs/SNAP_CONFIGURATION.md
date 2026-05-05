# Hermes Agent Snap Configuration Guide

This document covers all the ways you can configure the Hermes Agent when installed as a Snap.

## 1. Snap Set Commands (Easiest)
You can configure settings and manage services directly using the `snap set` command. This is ideal for remote management via tools like Canonical Landscape.

### Configuration Settings
| Key | Command Example | Description |
|-----|-----------------|-------------|
| `openai-api-key` | `sudo snap set hermes-agent openai-api-key=sk-...` | OpenAI API Key (stored in `.env`) |
| `anthropic-api-key` | `sudo snap set hermes-agent anthropic-api-key=sk-...` | Anthropic API Key (stored in `.env`) |
| `gemini-api-key` | `sudo snap set hermes-agent gemini-api-key=AI...` | Gemini API Key (stored in `.env`) |
| `deepseek-api-key` | `sudo snap set hermes-agent deepseek-api-key=sk-...` | DeepSeek API Key (stored in `.env`) |
| `log-level` | `sudo snap set hermes-agent log-level=DEBUG` | Sets logging verbosity in `config.yaml` |
| `model` | `sudo snap set hermes-agent model=gpt-4o` | Sets the default AI model in `config.yaml` |
| `personality` | `sudo snap set hermes-agent personality=professional` | Sets the agent personality in `config.yaml` |

### Service Management
You can enable or disable background services. By default, they are disabled.

| Service | Enable Command | Disable Command |
|---------|----------------|-----------------|
| **Background Agent** | `sudo snap set hermes-agent agent-service=enabled` | `sudo snap set hermes-agent agent-service=disabled` |
| **ACP Server** | `sudo snap set hermes-agent acp-service=enabled` | `sudo snap set hermes-agent acp-service=disabled` |

Check status: `snap services hermes-agent`
View logs: `sudo snap logs -f hermes-agent.agent`

## 2. Configuration Files
For advanced manual configuration:

*   **Location:** `/var/snap/hermes-agent/common/`
*   **Main Config:** `config.yaml`
*   **Env Variables:** `.env`

## 3. Snap Interfaces (Permissions)
| Interface | Command to Connect |
|-----------|--------------------|
| **Home** | `sudo snap connect hermes-agent:home` |
| **SSH Keys** | `sudo snap connect hermes-agent:ssh-keys` |
| **Removable** | `sudo snap connect hermes-agent:removable-media` |
| **Hidden Files**| `sudo snap connect hermes-agent:dot-hermes` |

## 4. Aliases
The Snap provides: `hermes`, `hermes.agent`, `hermes.acp`.

## 5. Setup Wizard
Run interactively: `hermes setup`

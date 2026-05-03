# Hermes Agent Snap Configuration Guide

This document covers all the ways you can configure the Hermes Agent when installed as a Snap.

## 1. Snap Set Commands (Easiest)
You can configure the most common settings directly using the `snap set` command.

| Setting | Command | Description |
|---------|---------|-------------|
| **OpenAI Key** | `sudo snap set hermes-agent openai-api-key=sk-...` | Sets the OpenAI API key in `.env` |
| **Anthropic Key** | `sudo snap set hermes-agent anthropic-api-key=sk-...` | Sets the Anthropic API key in `.env` |
| **Gemini Key** | `sudo snap set hermes-agent gemini-api-key=AI...` | Sets the Gemini API key in `.env` |
| **Log Level** | `sudo snap set hermes-agent log-level=DEBUG` | Sets the logging verbosity |

## 2. Configuration Files
For advanced configuration, you can edit the configuration files directly in the Snap's writable area.

*   **Config Location:** `/var/snap/hermes-agent/common/`
*   **Main Config:** `/var/snap/hermes-agent/common/config.yaml`
*   **Env Variables:** `/var/snap/hermes-agent/common/.env`

> **Note:** These files are persistent across Snap updates.

## 3. Snap Interfaces (Permissions)
Snaps run in a sandbox. You must explicitly grant permissions for certain tasks.

| Interface | Command to Connect | Description |
|-----------|--------------------|-------------|
| **Home** | `sudo snap connect hermes-agent:home` | Allows reading/writing files in your home directory. |
| **SSH Keys** | `sudo snap connect hermes-agent:ssh-keys` | Allows the agent to use your git/ssh keys. |
| **Removable** | `sudo snap connect hermes-agent:removable-media` | Allows access to USB drives and external disks. |
| **Hidden Files**| `sudo snap connect hermes-agent:dot-hermes` | Allows sharing data with an existing `~/.hermes` folder. |

## 4. Aliases
The Snap provides the following commands:

*   `hermes`: The main CLI for interacting with the agent.
*   `hermes.agent`: Runs the background agent process.
*   `hermes.acp`: Runs the Agent Client Protocol server.

If the aliases are not automatically enabled, you can enable them manually:
```bash
sudo snap alias hermes-agent.hermes hermes
```

## 5. Setup Wizard
You can also run the interactive setup wizard bundled with Hermes:
```bash
hermes setup
```

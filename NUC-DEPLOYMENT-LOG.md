# NUC Deployment Log — all-dev-hermesagent

This document records the exact sequence of commands used to install and configure the **all-dev-hermesagent** snap on the target NUC (Basil-nuc).

## 1. Installation
The snap was installed from the local build artifact:
```bash
sudo snap install --dangerous all-dev-hermesagent_0.12.0_amd64.snap
```

## 2. Interface Connections
Required security interfaces were connected manually to grant necessary system permissions:
```bash
sudo snap connect all-dev-hermesagent:removable-media :removable-media
sudo snap connect all-dev-hermesagent:ssh-keys :ssh-keys
```



## 3. Service Initialization
All internal services were enabled to ensure they start automatically on system boot:
```bash
sudo snap set all-dev-hermesagent acp-service=enabled
sudo snap set all-dev-hermesagent agent-service=enabled
sudo snap set all-dev-hermesagent gateway-service=enabled
sudo snap set all-dev-hermesagent dashboard-service=enabled
```

## 4. Control Tower (CT) Integration
The snap was bridged to the Control Tower mock server (192.168.1.6) for status reporting:
```bash
sudo snap set all-dev-hermesagent ct-callback-url=http://192.168.1.6:8080/callback
sudo snap set all-dev-hermesagent ct-deployment-id=deploy-001
sudo snap set all-dev-hermesagent ct-node-id=node-001
sudo snap set all-dev-hermesagent ct-snap-name=all-dev-hermesagent
```

## 5. Final Verification
The services were restarted to apply all configuration changes:
```bash
sudo snap restart all-dev-hermesagent
```

### Verification Commands
The following commands were used to verify the operational state:
```bash
# Check service status
snap services all-dev-hermesagent

# Verify configuration keys
snap get all-dev-hermesagent

# Verify interface connections
snap connections all-dev-hermesagent
```

---
**Status**: DEPLOYED & VERIFIED
**Environment**: Basil-nuc (Ubuntu Core / amd64)

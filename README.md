# Ansible Home Server Configuration

Ansible roles and playbooks for managing home servers and automation infrastructure. All services run as containers managed via Podman Quadlet systemd units (except Nextcloud AIO which uses Docker).

## Overview

This repository contains Ansible configurations for managing various services across multiple servers including:

- **Home Automation**: Home Assistant with Zigbee2MQTT and MQTT broker
- **Cloud Connectivity**: Cloudflared tunnel for secure remote access
- **Cloud Storage**: Nextcloud AIO for file synchronization and sharing
- **Development Environment**: Claude Code deployment

## Quick Start

```bash
# 1. Clone and configure secrets
cp vars/secrets.template.yml vars/secrets.yml
# Edit vars/secrets.yml with your values

# 2. Deploy everything to the automation server
ansible-playbook automation.yml
```

## Playbooks

### `automation.yml`
Main playbook targeting the `automation` host group. Deploys all roles in order:
1. `mqtt` - MQTT broker (required by zigbee2mqtt)
2. `zigbee2mqtt` - Zigbee bridge (requires mqtt)
3. `home-assistant` - Home automation (uses host networking)
4. `cloudflared` - Cloudflare tunnel
5. `nextcloud-aio` - Nextcloud (Docker-based)
6. `claude-code` - Claude Code environment

## Roles

| Role | Port(s) | Docs |
|------|---------|------|
| `mqtt` | 1883, 9001 | [docs/mqtt.md](docs/mqtt.md) |
| `zigbee2mqtt` | 20000 | [docs/zigbee2mqtt.md](docs/zigbee2mqtt.md) |
| `home-assistant` | 8123 (host network) | [docs/home-assistant.md](docs/home-assistant.md) |
| `cloudflared` | — (host network) | [docs/cloudflared.md](docs/cloudflared.md) |
| `nextcloud-aio` | 8080, 11000 | [docs/nextcloud-aio.md](docs/nextcloud-aio.md) |
| `claude-code` | — | [docs/claude-code.md](docs/claude-code.md) |

## Inventory

Server configurations in `inventory/`:

| File | Host | Description |
|------|------|-------------|
| `automation.yml` | `192.168.1.21` | Main automation server |

## Setup

### Prerequisites
- Ansible 2.9+
- SSH access to target servers
- Python 3.6+ on target hosts

### Configuration

1. **Secrets**: Create `vars/secrets.yml` from the template:
   ```bash
   cp vars/secrets.template.yml vars/secrets.yml
   ```

   Required variables:
   | Variable | Used by |
   |----------|---------|
   | `MQTT_PASSWORD` | mqtt, zigbee2mqtt |
   | `MQTT_AUTH_STRING` | mqtt |
   | `CLOUDFLARE_TOKEN` | cloudflared |
   | `ANTHROPIC_API_KEY` | claude-code |

2. **Inventory**: Update inventory files with your server IPs/hostnames

### Running Playbooks

Deploy all services:
```bash
ansible-playbook automation.yml
```

Deploy a single role:
```bash
ansible-playbook automation.yml --tags mqtt
```

Check what would change (dry run):
```bash
ansible-playbook automation.yml --check --diff
```

## Architecture

All container services use **Podman Quadlet** for systemd integration, meaning containers are managed as regular systemd units. The exception is Nextcloud AIO, which requires Docker due to its architecture.

```
automation server (192.168.1.21)
├── mqtt (Mosquitto)         ← Podman Quadlet
│   └── :1883
├── zigbee2mqtt              ← Podman Quadlet
│   ├── :20000 (frontend)
│   └── /dev/ttyUSB0 (Zigbee adapter)
├── home-assistant           ← Podman Quadlet (host network)
│   └── :8123
├── cloudflared              ← Podman Quadlet (host network)
├── nextcloud-aio            ← Docker (systemd service)
│   ├── :8080 (admin)
│   └── :11000 (web)
└── claude-code              ← Podman Quadlet (built locally)
```

## Security

- Secrets are managed through `vars/secrets.yml` (not version controlled)
- All sensitive data uses Ansible vault or environment variables
- SSH key authentication is required for remote access
- Claude Code container runs with `NoNewPrivileges=true` and all capabilities dropped

## License

Private repository for personal use

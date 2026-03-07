# Home Assistant

Home automation platform running in host network mode for device discovery.

## Configuration

### Default Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `home_assistant_version` | `2026.2.3` | Container image version |
| `home_assistant_image` | `ghcr.io/home-assistant/home-assistant` | Container image |
| `home_assistant_data_dir` | `/var/homeassistant/config` | Config directory on host |
| `home_assistant_port` | `8123` | Web UI port |
| `home_assistant_timezone` | `Europe/Amsterdam` | Container timezone |
| `home_assistant_network_mode` | `host` | Network mode |

## Ports

| Port | Description |
|------|-------------|
| 8123 | Web UI (host network) |

## File Locations

| Path (on host) | Description |
|-----------------|-------------|
| `/var/homeassistant/config/` | Configuration directory |
| `/etc/containers/systemd/homeassistant.container` | Quadlet unit file |

## Container Details

- **Image**: `ghcr.io/home-assistant/home-assistant:2026.2.3`
- **Network**: host mode (required for mDNS/SSDP discovery)
- **Systemd unit**: `homeassistant.service`
- **Auto-update**: enabled (registry)

## Management

```bash
# Service status
systemctl status homeassistant

# Restart
systemctl restart homeassistant

# View logs
journalctl -u homeassistant -f
```

## Notes

- Uses host networking so Home Assistant can discover devices on the local network
- Initial setup (creating admin account, configuring integrations) is done through the web UI at `http://<server-ip>:8123`
- MQTT integration should be configured to connect to the local Mosquitto broker
- Config directory persists across container restarts

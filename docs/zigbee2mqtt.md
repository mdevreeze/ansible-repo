# Zigbee2MQTT

Zigbee to MQTT bridge that exposes Zigbee devices to Home Assistant via the MQTT broker.

## Configuration

### Default Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `zigbee2mqtt_version` | `2.8.0` | Container image version |
| `zigbee2mqtt_image` | `docker.io/koenkk/zigbee2mqtt` | Container image |
| `zigbee2mqtt_user` | `1000` | Container user ID |
| `zigbee2mqtt_data_dir` | `/var/home/core/zigbee2mqtt` | Data directory on host |
| `zigbee2mqtt_port` | `20000` | Web frontend port |
| `zigbee2mqtt_timezone` | `Europe/Amsterdam` | Container timezone |
| `zigbee2mqtt_device` | `/dev/ttyUSB0` | Zigbee USB adapter device path |

### Secrets

| Variable | Description |
|----------|-------------|
| `MQTT_PASSWORD` | Used for MQTT broker connection and frontend auth token |

## Ports

| Port | Description |
|------|-------------|
| 20000 | Web frontend |

## File Locations

| Path (on host) | Description |
|-----------------|-------------|
| `{{ zigbee2mqtt_data_dir }}/` | Data and config directory |
| `{{ zigbee2mqtt_data_dir }}/configuration.yaml` | Main configuration |
| `/etc/containers/systemd/zigbee2mqtt.container` | Quadlet unit file |

## Container Details

- **Image**: `docker.io/koenkk/zigbee2mqtt:2.8.0`
- **Systemd unit**: `zigbee2mqtt.service`
- **Auto-update**: enabled (registry)
- **SELinux**: disabled (`SecurityLabelDisable=true`) for device access
- **Device passthrough**: USB Zigbee adapter (`/dev/ttyUSB0`)

## Management

```bash
# Service status
systemctl status zigbee2mqtt

# Restart
systemctl restart zigbee2mqtt

# View logs
journalctl -u zigbee2mqtt -f
```

## Dependencies

- **mqtt** role must be deployed first (MQTT broker connection)
- The container user must be in the `dialout` group for USB device access
- The Zigbee USB adapter must be connected at the configured device path

## Notes

- The frontend is protected with an auth token (uses `MQTT_PASSWORD`)
- Home Assistant discovery is enabled with `homeassistant` topic
- `permit_join` is disabled by default - enable temporarily via frontend to pair new devices
- Device list is managed in the `configuration.yaml.j2` template

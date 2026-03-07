# MQTT (Mosquitto)

Eclipse Mosquitto MQTT broker for IoT device communication.

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `MQTT_PASSWORD` | *(secret)* | Broker authentication password |
| `MQTT_AUTH_STRING` | *(secret)* | Auth credentials file content |

## Ports

| Port | Protocol | Description |
|------|----------|-------------|
| 1883 | MQTT | Standard MQTT |
| 9001 | WebSocket | MQTT over WebSocket |

## File Locations

| Path (on host) | Description |
|-----------------|-------------|
| `/var/home/core/mqtt/` | Config directory |
| `/var/home/core/mqtt/mosquitto.conf` | Mosquitto configuration |
| `/var/home/core/mqtt/auth.txt` | Password file |
| `/etc/containers/systemd/mqtt.container` | Quadlet unit file |

## Container Details

- **Image**: `docker.io/eclipse-mosquitto:2.0.22`
- **User**: `1883:1883`
- **Systemd unit**: `mqtt.service`
- **Auto-update**: enabled (registry)

## Management

```bash
# Service status
systemctl status mqtt

# Restart
systemctl restart mqtt

# View logs
journalctl -u mqtt -f
```

## Notes

- The broker uses file-based authentication via `auth.txt`
- Logging goes to `/mosquitto/log/mosquitto.log` inside the container
- Used by Zigbee2MQTT to publish device states to Home Assistant

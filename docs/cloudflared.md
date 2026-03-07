# Cloudflared

Cloudflare Tunnel client for secure remote access to services without exposing ports to the internet.

## Configuration

### Default Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `cloudflared_version` | `2025.11.1` | Container image version |
| `cloudflared_image` | `docker.io/cloudflare/cloudflared` | Container image |
| `cloudflared_user` | `1000` | Container user ID |
| `cloudflared_data_dir` | `/var/lib/cloudflared` | Config directory on host |
| `cloudflared_log_dir` | `/var/log/cloudflared` | Log directory on host |
| `cloudflared_tunnel_id` | *(set in defaults)* | Tunnel UUID |

### Secrets

| Variable | Description |
|----------|-------------|
| `CLOUDFLARE_TOKEN` | Tunnel authentication token |

## File Locations

| Path (on host) | Description |
|-----------------|-------------|
| `/var/lib/cloudflared/` | Config directory |
| `/var/log/cloudflared/` | Log directory |
| `/etc/containers/systemd/cloudflared.container` | Quadlet unit file |

## Container Details

- **Image**: `docker.io/cloudflare/cloudflared:2025.11.1`
- **Network**: host mode
- **Systemd unit**: `cloudflared.service`
- **Auto-update**: enabled (registry)

## Management

```bash
# Service status
systemctl status cloudflared

# Restart
systemctl restart cloudflared

# View logs
journalctl -u cloudflared -f
```

## Setup

1. Create a Cloudflare Tunnel in the [Cloudflare Zero Trust dashboard](https://one.dash.cloudflare.com/)
2. Copy the tunnel token
3. Set `CLOUDFLARE_TOKEN` in `vars/secrets.yml`
4. Configure public hostnames and routing rules in the Cloudflare dashboard

## Notes

- The tunnel runs with `--no-autoupdate` since updates are managed via container image updates
- Uses host networking to access all local services
- Tunnel routing (which services are exposed) is configured in the Cloudflare dashboard, not locally

# Nextcloud AIO

Nextcloud All-in-One deployment for file synchronization and sharing. Unlike other roles, this uses Docker (not Podman) because Nextcloud AIO manages its own sub-containers.

## Configuration

### Default Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `nextcloud_aio_version` | `latest` | Container image version |
| `nextcloud_aio_image` | `docker.io/nextcloud/all-in-one` | Container image |
| `nextcloud_aio_user` | `root` | Service user (requires root) |
| `nextcloud_aio_data_dir` | `/var/lib/nextcloud-aio` | Data directory on host |
| `nextcloud_aio_docker_socket` | `/var/run/docker.sock` | Docker socket path |
| `nextcloud_aio_port` | `8080` | Admin interface port |
| `nextcloud_aio_volume` | `nextcloud_aio_mastercontainer` | Docker volume name |
| `nextcloud_aio_apache_ip_binding` | `0.0.0.0` | Apache bind address |
| `nextcloud_aio_apache_port` | `11000` | Apache web port |

## Ports

| Port | Description |
|------|-------------|
| 8080 | AIO admin interface |
| 11000 | Nextcloud web interface (Apache) |

## File Locations

| Path (on host) | Description |
|-----------------|-------------|
| `/var/lib/nextcloud-aio/` | Data directory |
| `/etc/systemd/system/nextcloud-aio.service` | Systemd service file |

## Service Details

- **Image**: `docker.io/nextcloud/all-in-one:latest`
- **Runtime**: Docker (not Podman)
- **Systemd unit**: `nextcloud-aio.service`
- **Privileged**: yes (required by Nextcloud AIO)

## Management

```bash
# Service status
systemctl status nextcloud-aio

# Restart
systemctl restart nextcloud-aio

# View logs
journalctl -u nextcloud-aio -f

# View sub-containers managed by Nextcloud AIO
docker ps --filter "name=nextcloud-aio"
```

## Setup

1. Deploy the role
2. Access the admin interface at `http://<server-ip>:8080`
3. Follow the Nextcloud AIO setup wizard to configure storage, domains, and optional containers

## Notes

- Runs as a **privileged** container because Nextcloud AIO manages Docker sub-containers
- The master container pulls the image on every start (`ExecStartPre` does `docker pull`)
- If using Cloudflared for external access, configure the tunnel to point to port 11000
- Docker must be installed and running on the target host (`docker.service` is a dependency)

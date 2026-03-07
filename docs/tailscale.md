# Tailscale

Tailscale VPN mesh network client for secure remote access. Enables SSH access from any device on the tailnet (e.g., phone via Termius) without exposing ports to the internet.

## Configuration

### Default Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `tailscale_version` | `v1.80.3` | Container image version |
| `tailscale_image` | `ghcr.io/tailscale/tailscale` | Container image |
| `tailscale_state_dir` | `/var/lib/tailscale` | State directory on host |
| `tailscale_hostname` | `automation` | Tailscale network hostname |
| `tailscale_ssh` | `true` | Enable Tailscale SSH |

### Secrets

| Variable | Description |
|----------|-------------|
| `TAILSCALE_AUTH_KEY` | Auth key from Tailscale admin console |

## File Locations

| Path (on host) | Description |
|-----------------|-------------|
| `/var/lib/tailscale/` | State directory (node identity) |
| `/etc/containers/systemd/tailscale.container` | Quadlet unit file |

## Container Details

- **Image**: `ghcr.io/tailscale/tailscale:v1.80.3`
- **Network**: host mode
- **Capabilities**: `NET_ADMIN`, `NET_RAW`
- **Devices**: `/dev/net/tun`
- **Systemd unit**: `tailscale.service`
- **Auto-update**: enabled (registry)

## Management

```bash
# Service status
systemctl status tailscale

# Restart
systemctl restart tailscale

# View logs
journalctl -u tailscale -f

# Check Tailscale status
podman exec tailscale tailscale status
```

## Setup

1. Go to the [Tailscale admin console](https://login.tailscale.com/admin/settings/keys)
2. Generate an auth key (use a reusable key so re-authentication is automatic)
3. Set `TAILSCALE_AUTH_KEY` in `vars/secrets.yml`
4. Run the playbook: `ansible-playbook automation.yml`
5. Verify the node appears in the Tailscale admin console

## Phone Access to Claude Code

Once Tailscale is running on both the server and your phone:

1. Install Tailscale on your phone and sign in
2. Install an SSH client (e.g. Termius)
3. SSH to the server using its Tailscale IP or hostname: `ssh core@automation`
   - Tailscale SSH handles auth — no SSH key setup needed
4. Attach to the Claude Code session: `sudo podman attach claude-code`
5. To detach without stopping: press `Ctrl+P` then `Ctrl+Q`

## Notes

- Tailscale SSH (`--ssh`) eliminates the need to manage SSH keys — authentication is handled by Tailscale identity
- The auth key is only used for initial registration; state persists in `/var/lib/tailscale`
- A reusable auth key is recommended so the node can re-authenticate after state loss
- The container runs as root (required for network/TUN device management)

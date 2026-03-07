# Claude Code

Containerized Claude Code development environment, built locally from a Dockerfile and managed via Podman Quadlet.

## Configuration

### Default Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `claude_code_image` | `localhost/claude-code` | Container image (built locally) |
| `claude_code_version` | `latest` | Image tag |
| `claude_code_build_dir` | `/var/lib/claude-code/build` | Dockerfile location on host |
| `claude_code_workspace_volume` | `claude-code-workspace` | Named volume for workspace |
| `claude_code_user` | `1000` | Container user ID |
| `claude_code_env_file` | `/etc/claude-code/env` | Environment file with API key |

### Secrets

| Variable | Description |
|----------|-------------|
| `ANTHROPIC_API_KEY` | Claude API key |

## File Locations

| Path (on host) | Description |
|-----------------|-------------|
| `/etc/claude-code/env` | API key env file (mode 0600) |
| `/var/lib/claude-code/build/Dockerfile` | Build context |
| `/etc/containers/systemd/claude-code.container` | Quadlet container unit |
| `/etc/containers/systemd/claude-code.build` | Quadlet build unit |

## Container Details

- **Image**: locally built from Dockerfile
- **Systemd units**: `claude-code-build.service` (build), `claude-code.service` (run)
- **Security**: `NoNewPrivileges=true`, all capabilities dropped
- **Workspace**: persisted in named volume `claude-code-workspace`

## Management

```bash
# Service status
systemctl status claude-code

# Attach to interactive session
podman attach claude-code

# Restart
systemctl restart claude-code

# Rebuild image
systemctl restart claude-code-build

# View logs
journalctl -u claude-code -f
```

## Architecture

The role deploys two systemd units:

1. **claude-code-build.service** - Builds the container image from the Dockerfile
2. **claude-code.service** - Runs the container (depends on build service)

The container runs `claude --dangerously-skip-permissions` and keeps stdin open for interactive attachment via `podman attach`.

## Security

- API key is stored in `/etc/claude-code/env` with mode `0600` (root-only)
- Container runs as non-root user (UID 1000)
- All Linux capabilities are dropped
- `NoNewPrivileges` prevents privilege escalation
- No host filesystem access - workspace is an isolated named volume

# Dockge Stack

This stack provides a Docker Compose stack management interface.

## Services

### dockge
- **Image**: `louislam/dockge:1`
- **Purpose**: Web-based Docker Compose stack manager
- **Port**: `5001`
- **Features**:
  - Manage multiple Docker Compose stacks
  - Interactive terminal for each stack
  - Visual stack status
  - Edit compose files in browser
  - Start/stop/restart stacks

## Required Environment Variables

- `DOCKGE_STACKS_DIR`: Directory where Docker Compose stacks are stored (set to `/opt/homelab/stacks`)

## Port Mappings

- `5001` - Dockge web interface

## Volume Mounts

- `/var/run/docker.sock` - Docker socket for container management
- `/opt/appdata/dockge` - Dockge application data
- `/opt/homelab/stacks` - Stacks directory (must match on both sides)

**Important**: The stacks directory mount must be identical on both sides (left and right of the colon). This is a Dockge requirement.

## Networks

- `backend`: For internal communication
- `frontend`: For Traefik access

## Critical Configuration Note

⚠️ **Stacks Directory Mounting**

The stacks directory must be mounted with **identical paths** on both sides:
```yaml
volumes:
  - /opt/homelab/stacks:/opt/homelab/stacks
```

This requirement is for Dockge to properly:
1. Read compose files
2. Execute docker compose commands
3. Track stack states

**Do NOT use**:
```yaml
# ❌ WRONG - Different paths
volumes:
  - /opt/homelab/stacks:/stacks
```

## Features

### Stack Management
- Create new stacks with built-in editor
- Import existing Docker Compose files
- Start, stop, restart, and delete stacks
- View logs in real-time
- Access container terminals

### File Editing
- Built-in code editor for compose files
- Syntax highlighting
- Validation before deployment

### Monitoring
- Stack status overview
- Container status within stacks
- Resource usage per stack

## Security Considerations

- Dockge has full Docker socket access
- Can manage ALL containers on the system
- Ensure proper access control (recommend Traefik auth)
- Consider using Docker socket proxy for enhanced security

## Usage

### Initial Access
1. Navigate to `http://<server-ip>:5001`
2. Create admin account on first access
3. Start managing stacks

### Creating a New Stack
1. Click "Compose" → "New Stack"
2. Enter stack name
3. Write or paste docker-compose.yaml content
4. Click "Deploy"

### Managing Existing Stacks
All stacks in `/opt/homelab/stacks` are automatically discovered and displayed.

### Editing Stacks
1. Click on stack name
2. Click "Edit Compose"
3. Make changes
4. Click "Save" and "Deploy" to apply

## Integration with This Repository

If using this repository's stacks:
1. Clone repository to `/opt/homelab`
2. Dockge will automatically detect stacks in `stacks/` directory
3. Manage all homelab services from one interface

## Comparison to Portainer

**Dockge advantages:**
- Lightweight and fast
- Focused on Docker Compose
- Better compose file editing experience
- Simpler interface

**Portainer advantages:**
- More features (images, volumes, networks management)
- Stack templates
- Team management
- Multi-server support

Choose Dockge for simplicity and Compose-focused management.

## Backup

Backup Dockge data:
```bash
tar -czf dockge-backup.tar.gz /opt/appdata/dockge
```

Stacks are already in your repository, so they're version controlled.

## Notes

- Restart policy: `unless-stopped`
- Requires Docker socket access (full permissions)
- Stacks directory path must match on both sides of mount
- All stacks must be in the configured stacks directory
- Changes made in Dockge are immediately reflected on disk

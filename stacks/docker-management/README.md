# Docker Management Stack

This stack provides container monitoring, updates, and remote management capabilities.

## Services

### watchtower
- **Image**: `containrrr/watchtower`
- **Purpose**: Automatic Docker container updates
- **Features**:
  - Monitors running containers for image updates
  - Automatically pulls new images and recreates containers
  - Cleans up old images after update
  - HTTP API for metrics and control
  - Scheduled updates (4 AM daily)

### portainer-agent
- **Image**: `portainer/agent:2.30.1`
- **Purpose**: Agent for Portainer Server
- **Port**: `9001`
- **Features**:
  - Connects to Portainer Server for remote management
  - Provides container, volume, and network information
  - NVIDIA GPU awareness
  - Secure communication with Portainer Server

## Required Environment Variables

### Watchtower
- `API_TOKEN`: Authentication token for Watchtower HTTP API

### Portainer Agent
No environment variables required, but GPU variables are set for NVIDIA GPU visibility.

## Port Mappings

- `9001` - Portainer Agent communication port

## Volume Mounts

### Watchtower
- `/var/run/docker.sock` - Docker socket for container management

### Portainer Agent
- `/var/run/docker.sock` - Docker socket for container management
- `/var/lib/docker/volumes` - Access to Docker volumes
- `/` - Root filesystem access (mounted to `/host` in container)

## Networks

- `backend`: For internal communication
- `frontend`: For external access if needed

## Watchtower Configuration

### Schedule
- **Cron**: `0 0 4 * * *` (4:00 AM daily)
- Updates run once per day at 4 AM

### Features Enabled
- `WATCHTOWER_CLEANUP=true`: Removes old images after updates
- `WATCHTOWER_HTTP_API_METRICS=true`: Exposes metrics endpoint
- `WATCHTOWER_HTTP_API_TOKEN`: Secures API access

### Monitoring Updates
Check Watchtower logs:
```bash
docker logs watchtower
```

### Manual Update Trigger
Using the HTTP API (with your API_TOKEN):
```bash
curl -H "Authorization: Bearer <API_TOKEN>" \
  http://localhost:8080/v1/update
```

## Portainer Agent Configuration

### GPU Support
Configured with NVIDIA GPU support:
- `NVIDIA_DRIVER_CAPABILITIES=all`
- `NVIDIA_VISIBLE_DEVICES=all`
- Runtime: `nvidia`

This allows Portainer to see and manage GPU resources.

### Connecting to Portainer Server
1. Access Portainer Server UI
2. Add new environment
3. Select "Agent"
4. Enter agent URL: `<server-ip>:9001`
5. Agent will provide container, volume, and network information

## Security Considerations

### Watchtower
- Has full Docker socket access
- Can update any container
- API is token-protected
- Consider excluding critical containers from auto-updates

### Excluding Containers from Updates
Add label to containers you don't want auto-updated:
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=false"
```

### Portainer Agent
- Full Docker socket access
- Access to volumes and host filesystem
- Should only be accessible from trusted networks
- Secure with firewall rules on port 9001

## Monitoring

### Watchtower Metrics
If HTTP API metrics are enabled, access metrics at:
```
http://localhost:8080/v1/metrics
```
(Requires API_TOKEN authentication)

### Portainer Agent Status
Check agent status in Portainer Server UI or via logs:
```bash
docker logs portainer-agent
```

## Update Strategy

### Automatic Updates (Watchtower)
**Advantages:**
- Always running latest versions
- Security patches applied automatically
- No manual intervention needed

**Disadvantages:**
- Potential for breaking changes
- No testing before deployment
- Possible downtime during updates

### Recommendations
For critical services:
1. Exclude from Watchtower using labels
2. Test updates in development environment
3. Update manually with backups

For non-critical services:
- Let Watchtower handle updates automatically
- Monitor logs after update window

## Alternative: Portainer Server

This stack uses Portainer Agent. For full Portainer Server:
- Deploy from `portainer-server` stack (if available)
- Or use hosted Portainer service
- Configure agent to connect to server

## Integration with Other Stacks

### Komodo
Prevent Komodo from being stopped:
```yaml
labels:
  - "komodo.skip"
```

### Critical Services
Add to critical services to prevent auto-updates:
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=false"
```

## Troubleshooting

### Watchtower Not Updating Containers
- Check schedule is correct
- Verify Docker socket access
- Check logs for errors
- Ensure no update-blocking labels

### Portainer Agent Connection Issues
- Verify port 9001 is accessible
- Check firewall rules
- Verify agent is running
- Check Portainer Server configuration

### GPU Not Visible in Portainer
- Verify NVIDIA drivers installed on host
- Check nvidia-docker runtime is available
- Restart agent container

## Notes

- Watchtower runs continuously but only checks for updates at scheduled time
- Portainer Agent requires Portainer Server to be useful
- Both services have significant permissions - secure appropriately
- Consider using Docker secrets for API_TOKEN
- GPU support allows managing GPU-enabled containers from Portainer

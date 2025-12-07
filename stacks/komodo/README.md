# Komodo Stack

This stack provides the Komodo infrastructure orchestration and deployment platform.

## Services

### mongo
- **Image**: `mongo`
- **Purpose**: MongoDB database for Komodo
- **Features**:
  - Stores Komodo configuration and state
  - Optimized with limited cache size (256MB)
  - Protected from Komodo's StopAllContainers action
- **Internal Port**: `27017`

### core
- **Image**: `ghcr.io/mbecker20/komodo:latest`
- **Purpose**: Komodo Core server
- **Access**: 
  - `https://komodo.clenchedgaming.com`
  - `https://komodo.martinellegard.dev`
- **Port**: `9120`
- **Features**:
  - Server and container management
  - Deployment automation
  - Build and sync workflows
  - Git repository integration

### periphery
- **Image**: `ghcr.io/mbecker20/periphery:latest`
- **Purpose**: Komodo Periphery agent
- **Features**:
  - Executes commands on behalf of Core
  - Manages local Docker resources
  - Handles deployments
  - Git repository management

## Required Environment Variables

### MongoDB
- `KOMODO_DB_USERNAME`: MongoDB root username
- `KOMODO_DB_PASSWORD`: MongoDB root password

### Komodo Core
- `KOMODO_DB_USERNAME`: Database username (same as MongoDB)
- `KOMODO_DB_PASSWORD`: Database password (same as MongoDB)

### Komodo Periphery
- `PERIPHERY_ROOT_DIRECTORY`: Base directory for Komodo data (default: `/etc/komodo`)

## Optional Environment Variables

- `COMPOSE_LOGGING_DRIVER`: Logging driver to use (default: local)
- `COMPOSE_KOMODO_IMAGE_TAG`: Specific image tag (default: latest)

## Port Mappings

- `9120` - Komodo Core web interface

## Volume Mounts

### MongoDB
- `mongo-data` - Database files
- `mongo-config` - MongoDB configuration

### Core
- `repo-cache` - Cached repository data for quick access

### Periphery
- `/var/run/docker.sock` - Docker socket for container management
- `/proc` - Process information
- `${PERIPHERY_ROOT_DIRECTORY}` - Komodo data directory (repos, stacks, SSL)

## Networks

- `default`: Internal communication between services
- `frontend`: External access via Traefik

## Special Labels

All services have the `komodo.skip` label to prevent Komodo from stopping its own infrastructure with the "Stop All Containers" action.

## Periphery Configuration

### Directory Structure
The periphery uses `PERIPHERY_ROOT_DIRECTORY` (default: `/etc/komodo`) for:
- **repos/**: Git repository clones
- **repos/homelab/docker-compose**: Docker Compose stacks
- **ssl/**: SSL certificates (key.pem, cert.pem)

### SSL Certificates
Periphery can use SSL for secure communication:
- Key file: `${PERIPHERY_ROOT_DIRECTORY}/ssl/key.pem`
- Cert file: `${PERIPHERY_ROOT_DIRECTORY}/ssl/cert.pem`

## Initial Setup

1. Start the stack
2. Access Komodo at one of the configured URLs
3. Complete first-time setup wizard
4. Configure Periphery connection if not auto-detected
5. Add servers, deployments, and builds

## Core Configuration

Optionally mount custom configuration:
```yaml
volumes:
  - /path/to/core.config.toml:/config/config.toml
```

## Periphery Deployment Options

### Containerized (Current)
- Deployed as Docker container
- Managed by Compose
- Easy to update and maintain

### Systemd Binary
Alternative deployment using systemd service:
- See: https://github.com/mbecker20/komodo/tree/main/scripts
- Better for multi-server environments
- Direct system integration

## Features

### Infrastructure Management
- Server management and monitoring
- Docker container orchestration
- Stack deployments
- Resource allocation

### Builds
- Git-based builds
- Docker image building
- Automated build triggers
- Build caching

### Procedures
- Scripted workflows
- Chained operations
- Conditional execution
- Scheduled tasks

### Syncs
- File synchronization
- Configuration management
- Template deployments

## Access Control

Komodo supports:
- User authentication
- Role-based access control (RBAC)
- API keys for automation
- Webhook integrations

## Logging

Configured with `COMPOSE_LOGGING_DRIVER` (default: local):
- Prevents log buildup
- Better performance
- Use `docker logs` to view

## Monitoring

Komodo provides monitoring for:
- Container status
- Resource usage
- Deployment history
- Build logs
- System health

## Security Considerations

- Change default MongoDB credentials
- Use SSL certificates for Periphery
- Secure access with strong passwords
- Consider network isolation for MongoDB
- Protect port 9120 with firewall or Traefik auth

## Backup

Important data to backup:
- MongoDB data volume: `mongo-data`
- Komodo configurations
- Repository cache if needed

```bash
# Backup MongoDB
docker exec mongo mongodump --username=${KOMODO_DB_USERNAME} \
  --password=${KOMODO_DB_PASSWORD} --out=/backup

# Backup volumes
docker run --rm -v komodo_mongo-data:/data -v $(pwd):/backup \
  alpine tar czf /backup/mongo-data.tar.gz /data
```

## Integration with Homelab

The periphery is configured to access stacks at:
```
/etc/komodo/repos/homelab/docker-compose
```

This allows Komodo to manage all homelab stacks if the repository is cloned to this location.

## Accessing via Host Docker

For Komodo Core to connect to host's Docker (if needed):
```yaml
extra_hosts:
  - host.docker.internal:host-gateway
```

Then configure Periphery at: `http://host.docker.internal:8120`

## Troubleshooting

### Can't Connect to Core
- Verify service is running
- Check port 9120 is accessible
- Review Traefik labels and routing

### MongoDB Connection Issues
- Verify credentials are correct
- Check MongoDB container is healthy
- Review network connectivity

### Periphery Not Responding
- Check Docker socket access
- Verify directory permissions
- Review periphery logs

## Notes

- MongoDB cache size limited to 256MB (adjust if needed)
- All services use quiet/reduced logging for performance
- Komodo can manage its own updates
- Periphery requires Docker socket access
- Consider dedicated network for production use

# Martin's Homelab

This repository contains Docker Compose configurations for a complete homelab infrastructure. The setup is organized into modular stacks, each serving a specific purpose within the homelab ecosystem.

## Repository Structure

```
martin-homelab/
├── ansible/          # Ansible playbooks for server maintenance and configuration
│   └── ubuntu/       # Ubuntu-specific playbooks (updates, reboots, SSH keys)
└── stacks/           # Docker Compose stacks for various services
    ├── brassbeard-apps/
    ├── dashboards/
    ├── databases/
    ├── dev/
    ├── docker-management/
    ├── dockge/
    ├── download-clients/
    ├── grid-compute/
    ├── komodo/
    ├── media-server/
    ├── nextcloud/
    ├── portainer-server/
    ├── reverse-proxy/
    ├── semaphoreui/
    ├── servarr/
    ├── server-monitor/
    ├── smarter-home/
    ├── surrealdb/
    └── tailscale-main-server/
```

## Purpose

This repository serves as a centralized configuration management system for a Docker-based homelab environment. It enables:

- **Infrastructure as Code**: All services are defined declaratively using Docker Compose
- **Version Control**: Track changes to service configurations over time
- **Easy Deployment**: Deploy and update services with simple Docker Compose commands
- **Modular Organization**: Each stack is self-contained and can be managed independently

## Quick Start

### Prerequisites

- Docker and Docker Compose installed
- Access to required networks (frontend/backend Docker networks should be created)
- Environment variables configured (see individual stack READMEs)

### Common Environment Variables

Most stacks require the following environment variable:
- `DOMAIN_NAME`: Your base domain name for the homelab (e.g., `example.com`)

Additional environment variables are specific to each stack. See the README in each stack folder for details.

### Deploying a Stack

Navigate to the desired stack directory and run:

```bash
cd stacks/<stack-name>
docker compose up -d
```

### Stopping a Stack

```bash
cd stacks/<stack-name>
docker compose down
```

## Stack Categories

### Infrastructure & Management
- **reverse-proxy**: Traefik reverse proxy for HTTPS and routing
- **docker-management**: Container management tools (Watchtower, Portainer Agent)
- **dockge**: Docker Compose stack management UI
- **portainer-server**: (Deprecated - see docker-management)
- **komodo**: Container orchestration and management platform

### Media & Entertainment
- **media-server**: Jellyfin, Plex, and Tautulli for media streaming
- **servarr**: Media automation (Sonarr, Radarr, Prowlarr, Overseerr, etc.)
- **download-clients**: Deluge and NZBGet with VPN

### Databases & Storage
- **databases**: PostgreSQL and TimescaleDB instances
- **surrealdb**: SurrealDB with TiKV backend
- **nextcloud**: Personal cloud storage

### Monitoring & Dashboards
- **dashboards**: Homepage dashboard and UPS monitoring
- **server-monitor**: Notifiarr for server notifications

### Development & Automation
- **dev**: Code-server (VS Code in browser)
- **semaphoreui**: Ansible automation UI
- **smarter-home**: Custom home automation backend

### Utilities
- **brassbeard-apps**: Chmod calculator utility
- **grid-compute**: Folding@home for distributed computing
- **tailscale-main-server**: VPN mesh networking

## Networks

The stacks use two primary Docker networks:
- **frontend**: For services that need to be accessible via Traefik
- **backend**: For internal service communication

Create these networks before deploying stacks:
```bash
docker network create frontend
docker network create backend
```

## Contributing

When making changes:
1. Update the relevant stack's README.md with any new environment variables
2. Use `${DOMAIN_NAME}` instead of hardcoded domain names
3. Ensure no sensitive information (passwords, tokens, API keys) is committed
4. Test changes locally before committing

## Security Notes

- Never commit actual credentials or API keys
- Use environment variables for all sensitive data
- Review `.env` and `stack.env` files before committing
- The `.gitignore` should exclude sensitive files

## Maintenance

See the `ansible/` directory for maintenance playbooks:
- System updates
- Reboot management
- SSH key management
- Disk space monitoring

## Documentation

Each stack has its own README.md file with:
- Service descriptions
- Required environment variables
- Port mappings
- Volume mounts
- Specific configuration instructions

Refer to individual stack READMEs for detailed information.
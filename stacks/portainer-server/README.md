# Portainer Server Stack

This stack directory is currently empty.

## Status

This stack has been replaced or is not in use. For container management, see:

- **docker-management** stack - Contains Portainer Agent for connecting to a Portainer Server instance
- **dockge** stack - Modern alternative for Docker Compose management

## Historical Context

Portainer Server was previously deployed here but has been moved or replaced with other management solutions.

## Alternatives

### For Docker Management
- **Dockge**: Lightweight Docker Compose manager with built-in editor
- **Portainer Agent**: Connect to existing Portainer Server installation
- **Komodo**: Advanced infrastructure orchestration platform

### If You Need Portainer Server

Deploy Portainer Server directly:

```yaml
version: "3.8"
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9443:9443"
      - "8000:8000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    networks:
      - backend
      - frontend
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.portainer.rule=Host(`portainer.${DOMAIN_NAME}`)"
      - "traefik.http.routers.portainer-secure.entrypoints=websecure"
      - "traefik.http.routers.portainer-secure.rule=Host(`portainer.${DOMAIN_NAME}`)"
      - "traefik.http.routers.portainer-secure.tls=true"
      - "traefik.http.services.portainer.loadbalancer.server.port=9443"
      - "traefik.http.services.portainer.loadbalancer.server.scheme=https"

volumes:
  portainer_data:

networks:
  backend:
    external: true
  frontend:
    external: true
```

Then connect the Portainer Agent from **docker-management** stack to this server.

## Notes

- This directory can be removed if not planning to use Portainer Server
- Portainer Agent in **docker-management** stack requires a Portainer Server to connect to
- Consider using Dockge for simpler Docker Compose management

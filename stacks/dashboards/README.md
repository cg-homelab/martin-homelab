# Dashboards Stack

This stack provides various dashboard and monitoring interfaces.

## Services

### dockerproxy
- **Image**: `tecnativa/docker-socket-proxy:latest`
- **Purpose**: Secure proxy for Docker socket access
- **Features**:
  - Read-only Docker API access
  - Restricts API endpoints for security
  - Used by Homepage for container information
- **Port**: `127.0.0.1:2375` (localhost only)

### homepage
- **Image**: `gethomepage/homepage:latest`
- **Purpose**: Unified dashboard for homelab services
- **Features**:
  - Displays service status and metrics
  - Integration with various platforms (Portainer, Plex, Sonarr, etc.)
  - Customizable widgets
- **Port**: `3000`

### ups-dashboard (PeaNUT)
- **Image**: `brandawg93/peanut:latest`
- **Purpose**: UPS monitoring dashboard
- **Features**:
  - Real-time UPS status
  - Power metrics
  - Battery information
- **Access**: `https://peanut.${DOMAIN_NAME}`
- **Port**: `3001`

## Required Environment Variables

### Core Variables
- `DOMAIN_NAME`: Base domain for service access
- `PUID`: User ID for Homepage (typically 1000)
- `PGID`: Group ID for Homepage (typically 1000)

### Homepage Configuration
- `HOMEPAGE_ALLOWED_HOSTS`: Comma-separated list of allowed hostnames

### Service Integration Variables

**TrueNAS**
- `HOMEPAGE_VAR_TRUENAS_URL`: TrueNAS web interface URL
- `HOMEPAGE_VAR_TRUENAS_API_KEY`: TrueNAS API key

**Portainer**
- `HOMEPAGE_VAR_PORTAINER_URL`: Portainer URL
- `HOMEPAGE_VAR_PORTAINER_API_KEY`: Portainer API key

**Unifi Network**
- `HOMEPAGE_VAR_UNIFI_NETWORK_URL`: Unifi controller URL
- `HOMEPAGE_VAR_UNIFI_NETWORK_USERNAME`: Unifi username
- `HOMEPAGE_VAR_UNIFI_NETWORK_PASSWORD`: Unifi password

**Tautulli (Plex Stats)**
- `HOMEPAGE_VAR_TAUTULLI_URL`: Tautulli URL
- `HOMEPAGE_VAR_TAUTULLI_API_KEY`: Tautulli API key

**UPS (PeaNUT)**
- `HOMEPAGE_VAR_PEANUT_URL`: PeaNUT URL
- `HOMEPAGE_VAR_PEANUT_USERNAME`: PeaNUT username
- `HOMEPAGE_VAR_PEANUT_PASSWORD`: PeaNUT password

**Sonarr**
- `HOMEPAGE_VAR_SONARR_URL`: Sonarr URL
- `HOMEPAGE_VAR_SONARR_API_KEY`: Sonarr API key

**Radarr**
- `HOMEPAGE_VAR_RADARR_URL`: Radarr URL
- `HOMEPAGE_VAR_RADARR_API_KEY`: Radarr API key

**Overseerr**
- `HOMEPAGE_VAR_OVERSEERR_URL`: Overseerr URL
- `HOMEPAGE_VAR_OVERSEERR_API_KEY`: Overseerr API key

**Deluge**
- `HOMEPAGE_VAR_DELUGE_URL`: Deluge URL
- `HOMEPAGE_VAR_DELUGE_PASSWORD`: Deluge web password

**NZBGet**
- `HOMEPAGE_VAR_NZBGET_URL`: NZBGet URL
- `HOMEPAGE_VAR_NZBGET_USERNAME`: NZBGet username
- `HOMEPAGE_VAR_NZBGET_PASSWORD`: NZBGet password

## Volume Mounts

- `/mnt/fast/appdata/homepage` - Homepage configuration and data
- `/mnt/fast/appdata/peanut` - PeaNUT configuration

## Networks

- `frontend`: For Traefik access (UPS dashboard)
- `backend`: For Docker socket proxy and internal communication

## Configuration

Homepage configuration files should be placed in `/mnt/fast/appdata/homepage/`:
- `services.yaml` - Service definitions
- `widgets.yaml` - Widget configuration
- `settings.yaml` - General settings
- `bookmarks.yaml` - Bookmark links

## Security Notes

- Docker socket proxy is configured for read-only access
- Proxy is only accessible on localhost (127.0.0.1)
- POST operations are disabled on the Docker socket
- All API keys and passwords should be stored as environment variables

## Notes

- Homepage aggregates information from multiple services
- Ensure all service URLs are accessible from the Homepage container
- API keys need appropriate permissions for the services they access
- UPS dashboard requires connection to a UPS device via network

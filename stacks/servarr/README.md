# Servarr Stack

This stack provides automated media management services (the *arr family).

## Services

### flaresolverr
- **Image**: `flaresolverr/flaresolverr:latest`
- **Purpose**: Bypass Cloudflare protection on indexers
- **Port**: `8191` (default, configurable via `PORT` env var)
- **Features**:
  - Proxy service for Prowlarr
  - Handles Cloudflare challenges
  - CAPTCHA solving support

### overseerr
- **Image**: `linuxserver/overseerr:latest`
- **Purpose**: Request management for Plex/Jellyfin
- **Access**: `https://overseerr.${DOMAIN_NAME}`
- **Port**: `5055`
- **Features**:
  - Media request interface for users
  - Integration with Plex/Jellyfin
  - Automatic request approval workflows
  - Integrates with Sonarr/Radarr

### prowlarr
- **Image**: `linuxserver/prowlarr:latest`
- **Purpose**: Indexer manager and proxy
- **Port**: `9696`
- **Features**:
  - Central management of torrent/usenet indexers
  - Syncs indexers to Sonarr/Radarr
  - Built-in indexer search

### readarr
- **Image**: `linuxserver/readarr:develop`
- **Purpose**: Book/ebook management
- **Access**: `https://readarr.${DOMAIN_NAME}`
- **Port**: `8787`
- **Features**:
  - Automated book downloading
  - Metadata management
  - Integration with download clients

### radarr
- **Image**: `linuxserver/radarr:latest`
- **Purpose**: Movie management and automation
- **Access**: `https://radarr.${DOMAIN_NAME}`
- **Port**: `7878`
- **Features**:
  - Automatic movie downloads
  - Quality upgrades
  - Calendar and notifications

### sonarr
- **Image**: `linuxserver/sonarr:latest`
- **Purpose**: TV series management and automation
- **Access**: `https://sonarr.${DOMAIN_NAME}`
- **Port**: `8989`
- **Features**:
  - Automatic TV show downloads
  - Episode monitoring
  - Quality upgrades

### tdarr
- **Image**: `haveagitgat/tdarr:latest`
- **Purpose**: Media transcoding and health checking
- **Ports**: `8265` (web UI), `8266` (server)
- **Features**:
  - Automated media transcoding
  - GPU-accelerated encoding
  - Media health checks
  - Plugin system for workflows

## Required Environment Variables

### Core Variables
- `DOMAIN_NAME`: Base domain for service access

### FlareSolverr (Optional)
- `LOG_LEVEL`: Logging level (default: info)
- `LOG_HTML`: Log HTML responses (default: false)
- `CAPTCHA_SOLVER`: CAPTCHA solving method (default: none)
- `PORT`: Service port (default: 8191)

## Port Mappings

- `8191` - FlareSolverr API
- `5055` - Overseerr web interface
- `9696` - Prowlarr web interface
- `8787` - Readarr web interface
- `7878` - Radarr web interface
- `8989` - Sonarr web interface
- `8265` - Tdarr web interface
- `8266` - Tdarr server port

## Volume Mounts

### Application Config
- `/mnt/fast/appdata/overseerr` - Overseerr configuration
- `/mnt/fast/appdata/prowlarr` - Prowlarr configuration
- `/mnt/fast/appdata/readarr` - Readarr configuration
- `/mnt/fast/appdata/radarr` - Radarr configuration
- `/mnt/fast/appdata/sonarr-anime` - Sonarr configuration

### Media Libraries
- `/mnt/array/media/books` - Books library (Readarr)
- `/mnt/array/media/movies` - Movies library (Radarr)
- `/mnt/array/media/series/anime` - Anime series (Sonarr)
- `/mnt/array/media/series/tv` - TV shows (Sonarr)

### Downloads
- `/mnt/array/downloads/` - Shared downloads directory (all *arr apps)

### Tdarr
- `/mnt/fast/appdata/tdarr/server` - Tdarr server data
- `/mnt/fast/appdata/tdarr/configs` - Tdarr configuration
- `/mnt/fast/appdata/tdarr/logs` - Tdarr logs
- `/mnt/array/media` - Media files for transcoding
- `/mnt/fast/appdata/tdarr-transcode` - Temporary transcoding directory

## GPU Requirements (Tdarr)

- NVIDIA GPU with appropriate drivers
- NVIDIA Container Toolkit configured
- Used for hardware-accelerated transcoding

## User/Group Configuration

All services use:
- `PUID=1000`
- `PGID=1000`
- `TZ=Europe/Oslo` (except Tdarr: Europe/London)

## Networks

- `frontend`: For Traefik access (Overseerr, Readarr, Radarr, Sonarr)
- `backend`: For internal communication and download client access

## Configuration Workflow

1. **Prowlarr**: Add indexers (torrent/usenet sites)
2. **Sonarr/Radarr/Readarr**: 
   - Add Prowlarr as indexer source
   - Configure download clients
   - Set quality profiles
3. **Overseerr**: 
   - Connect to Plex/Jellyfin
   - Add Sonarr/Radarr instances
   - Configure request permissions
4. **Tdarr**:
   - Configure libraries to transcode
   - Set up transcoding rules
   - Configure GPU settings

## FlareSolverr Integration

Configure Prowlarr to use FlareSolverr:
- FlareSolverr URL: `http://flaresolverr:8191`
- Use for indexers with Cloudflare protection

## Notes

- Ensure media and download directories exist with proper permissions
- All *arr apps should point to the same download client
- Use hard links by keeping media and downloads on the same filesystem
- Tdarr can be resource-intensive during transcoding
- Overseerr provides a user-friendly interface for media requests

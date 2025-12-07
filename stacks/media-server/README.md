# Media Server Stack

This stack provides media streaming capabilities with multiple platforms.

## Services

### jellyfin
- **Image**: `jellyfin/jellyfin`
- **Purpose**: Open-source media server for streaming movies and TV shows
- **Features**:
  - NVIDIA GPU acceleration for transcoding
  - Hardware-accelerated video playback
  - Web-based interface
- **Access**: `https://jellyfin.${DOMAIN_NAME}`

### plex
- **Image**: `linuxserver/plex:latest`
- **Purpose**: Popular media streaming platform
- **Features**:
  - NVIDIA GPU support for hardware transcoding
  - Network mode: host (for better discovery and performance)
  - Automatic metadata fetching
- **Note**: Runs in host network mode

### tautulli
- **Image**: `linuxserver/tautulli:latest`
- **Purpose**: Monitoring and statistics for Plex Media Server
- **Features**:
  - Usage statistics
  - Recently added media tracking
  - User activity monitoring
- **Access**: `https://tautulli.${DOMAIN_NAME}`

## Required Environment Variables

- `DOMAIN_NAME`: Base domain for service access

## Port Mappings

- `8096` - Jellyfin web interface
- `8181` - Tautulli web interface
- Plex uses host networking (no port mapping needed)

## Volume Mounts

### Jellyfin
- `/mnt/fast/appdata/jellyfin/config` - Configuration and database
- `/mnt/fast/appdata/jellyfin/cache` - Transcoding cache
- `/mnt/array/media/series` - TV shows library
- `/mnt/array/media/movies` - Movies library

### Plex
- `/mnt/fast/appdata/plex` - Configuration and database
- `/mnt/array/media/series` - TV shows library (mounted as /tv)
- `/mnt/array/media/movies` - Movies library (mounted as /movies)

### Tautulli
- `/mnt/fast/appdata/tautulli` - Configuration

## GPU Requirements

Both Jellyfin and Plex require:
- NVIDIA GPU with appropriate drivers installed
- NVIDIA Container Toolkit configured
- GPU runtime available to Docker

## User/Group Configuration

- Jellyfin: UID 568, GID 568 (with additional groups 107, 44 for hardware access)
- Plex: UID 1000, GID 1000
- Tautulli: UID 1000, GID 1000

## Networks

- `frontend`: For Traefik access (Jellyfin, Tautulli)
- `backend`: For internal communication (Tautulli to Plex)

## Timezone

All services use `TZ=Europe/Oslo`

## Notes

- Ensure media directories exist and have proper permissions
- GPU transcoding significantly reduces CPU usage
- Plex requires initial setup through its web interface
- Tautulli needs to be configured to connect to your Plex instance

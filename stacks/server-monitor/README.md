# Server Monitor Stack

This stack provides server monitoring and notification capabilities.

## Services

### notifiarr
- **Image**: `golift/notifiarr`
- **Purpose**: Notification and monitoring service for various applications
- **Port**: `5454`
- **Features**:
  - Integration with *arr applications (Sonarr, Radarr, etc.)
  - System statistics collection
  - Discord, Telegram, Slack notifications
  - Custom notification triggers
  - Plex session monitoring

## Required Environment Variables

No explicit environment variables are defined in the compose file. Configuration is typically done through:
1. Web interface after first start
2. Configuration file in the config volume
3. Notifiarr.com website integration

## Port Mappings

- `5454` - Notifiarr web interface

## Volume Mounts

- `/mnt/fast/appdata/notifiarr` - Configuration and data
- `/var/run/utmp` - System user information (for login tracking)
- `/etc/machine-id` - Unique machine identifier

## Networks

- `backend`: For communication with other services
- `frontend`: For external access

## Initial Setup

1. Access Notifiarr at `http://<server-ip>:5454`
2. Sign up or log in to Notifiarr.com
3. Get your API key from Notifiarr.com
4. Configure API key in local Notifiarr instance
5. Configure integrations with your services

## Supported Integrations

### Media Management
- **Sonarr**: TV show notifications
- **Radarr**: Movie notifications
- **Lidarr**: Music notifications
- **Readarr**: Book notifications
- **Prowlarr**: Indexer notifications

### Media Servers
- **Plex**: Session monitoring, library updates
- **Jellyfin**: Similar to Plex integration
- **Tautulli**: Enhanced Plex statistics

### Download Clients
- **qBittorrent**: Download notifications
- **Deluge**: Download notifications
- **NZBGet**: Usenet download notifications
- **SABnzbd**: Usenet download notifications

### System Monitoring
- CPU usage
- Memory usage
- Disk space
- Network statistics
- Service status

### Other Services
- **Overseerr**: Request notifications
- **Ombi**: Request notifications
- **Tautulli**: Plex playback notifications

## Notification Channels

Configure notifications via:
- **Discord**: Rich embeds with media posters
- **Telegram**: Text and image notifications
- **Slack**: Channel notifications
- **Email**: Traditional email alerts
- **Custom Webhooks**: Integration with any service

## Configuration

### Via Web Interface
1. Access `http://<server-ip>:5454`
2. Navigate to settings
3. Configure each integration:
   - Service URL
   - API key
   - Notification preferences

### Via Configuration File
Edit `/mnt/fast/appdata/notifiarr/notifiarr.conf`:
```
api_key = "your-notifiarr-api-key"
bind_addr = "0.0.0.0:5454"
# Add other configurations
```

## Notification Types

### Media Additions
- New episodes added to Sonarr
- New movies added to Radarr
- New books added to Readarr
- Library updates

### Download Events
- Download started
- Download completed
- Download failed
- Import completed

### System Alerts
- Disk space low
- Service offline
- High CPU/memory usage
- Update available

### Plex Sessions
- New stream started
- Stream stopped
- User joined/left
- Buffer warnings

## Custom Triggers

Create custom notification rules:
1. Define conditions (e.g., disk space < 10GB)
2. Select notification channel
3. Customize message format
4. Set notification frequency

## Integration with Homelab

### Connecting to Services
Ensure services are accessible:
- Use Docker network (`backend`)
- Or use service URLs via Traefik
- API keys must have appropriate permissions

### Example Sonarr Integration
1. In Notifiarr web UI:
   - URL: `http://sonarr:8989` or `https://sonarr.${DOMAIN_NAME}`
   - API Key: From Sonarr → Settings → General
2. In Sonarr:
   - Settings → Connect → Add Notifiarr
   - API Key: Your Notifiarr API key

## System Information Access

Volume mounts provide system info:
- `/var/run/utmp`: Active user sessions
- `/etc/machine-id`: Unique system identifier

This allows accurate system monitoring and statistics.

## Security Considerations

- Notifiarr API key should be kept secure
- Consider Traefik authentication for web interface
- Service API keys need read access to services
- Review notification content for sensitive data

## Monitoring Dashboard

Notifiarr.com provides:
- Centralized dashboard for all servers
- Historical notifications
- Service status overview
- Analytics and statistics

## Troubleshooting

### Can't Connect to Services
- Verify service URLs are correct
- Check API keys are valid
- Ensure services are on same network
- Review Notifiarr logs

### Notifications Not Sending
- Verify notification channel is configured
- Check webhook URLs are correct
- Review notification rules
- Check Notifiarr.com status

### High Resource Usage
- Review notification frequency
- Check system monitoring interval
- Reduce number of integrations
- Review logs for errors

## Backup

Backup Notifiarr configuration:
```bash
tar -czf notifiarr-backup.tar.gz /mnt/fast/appdata/notifiarr
```

Configuration includes:
- API keys
- Service integrations
- Notification rules
- Custom triggers

## Alternative: Standalone Notifications

Individual services also support their own notifications:
- Sonarr/Radarr: Built-in Connect feature
- Tautulli: Built-in notification agents
- Overseerr: Native Discord/Telegram support

Notifiarr centralizes and enhances these capabilities.

## Notes

- Restart policy: `unless-stopped`
- Hostname is set to `notifiarr` for identification
- Requires active Notifiarr.com account
- Free tier available with limitations
- Premium tier offers additional features
- System monitoring requires host mounts

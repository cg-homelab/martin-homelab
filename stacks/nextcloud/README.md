# Nextcloud Stack

This stack provides personal cloud storage and file synchronization.

## Services

### nextcloud
- **Image**: `linuxserver/nextcloud:latest`
- **Purpose**: Self-hosted cloud storage and collaboration platform
- **Port**: `4443` (HTTPS)
- **Features**:
  - File storage and synchronization
  - Calendar and contacts
  - Document collaboration
  - Photo gallery
  - Extensible via apps

## Required Environment Variables

No environment variables explicitly required, but may be needed for Nextcloud configuration.

## Port Mappings

- `4443:443` - HTTPS web interface

## Volume Mounts

- `/opt/appdata/nextcloud` - Nextcloud configuration
- `/opt/appdata/nextcloud-apps` - Custom Nextcloud apps
- `/mnt/nextcloud` - User data storage

## User/Group Configuration

- `PUID=1000`
- `PGID=1000`
- `TZ=Europe/Oslo`

## Networks

- `homelab`: External network for service communication

## Storage Organization

### Configuration
- **Path**: `/opt/appdata/nextcloud`
- **Contains**: Nextcloud system configuration, database

### Custom Apps
- **Path**: `/opt/appdata/nextcloud-apps`
- **Contains**: Additional Nextcloud applications not in default installation

### User Data
- **Path**: `/mnt/nextcloud`
- **Contains**: All user files, photos, documents

## Initial Setup

1. Access Nextcloud at `https://<server-ip>:4443`
2. Create admin account
3. Configure database (SQLite for simple setup, or external DB for production)
4. Set data directory (already configured to `/data`)
5. Complete installation

## Database Configuration

Nextcloud supports multiple database backends:
- **SQLite**: Built-in, good for small installations
- **PostgreSQL**: Recommended for production (use `databases` stack)
- **MySQL/MariaDB**: Alternative option

### Using PostgreSQL from Databases Stack
During setup:
1. Select PostgreSQL
2. Database host: `db-pg:5432`
3. Create database: `nextcloud`
4. Use credentials from database stack

## Nextcloud Apps

Extend functionality with official and community apps:
- Calendar & Contacts
- Talk (chat and video calls)
- Deck (kanban board)
- Notes
- Mail
- And many more

Install apps from the Nextcloud app store within the web interface.

## File Synchronization

### Desktop Clients
- Windows, macOS, Linux clients available
- Automatic file synchronization
- Selective sync support

### Mobile Apps
- iOS and Android apps
- Automatic photo upload
- Offline access

### WebDAV Access
Files accessible via WebDAV:
- URL: `https://<server-ip>:4443/remote.php/dav/files/<username>/`
- Use Nextcloud credentials

## Performance Optimization

### Caching
Consider adding Redis or Memcached for better performance:
```yaml
redis:
  image: redis:alpine
  container_name: nextcloud-redis
  restart: unless-stopped
```

Update Nextcloud config to use Redis for caching.

### Background Jobs
Configure cron for background jobs:
- Go to Administration → Basic settings
- Select "Cron" under background jobs
- Requires system cron or separate container

## Security Recommendations

1. **Strong Admin Password**: Use a strong password for admin account
2. **Two-Factor Authentication**: Enable 2FA for all users
3. **HTTPS Only**: Enforce HTTPS connections
4. **Regular Updates**: Keep Nextcloud updated
5. **File Access Control**: Use Nextcloud's sharing permissions

## Backup Strategy

Important directories to backup:
- `/opt/appdata/nextcloud` - Configuration and database
- `/mnt/nextcloud` - User data
- Database (if using external database)

Backup regularly:
```bash
# Backup user data
tar -czf nextcloud-data-backup.tar.gz /mnt/nextcloud

# Backup configuration
tar -czf nextcloud-config-backup.tar.gz /opt/appdata/nextcloud
```

## Updating Nextcloud

1. Backup data and configuration
2. Pull new image: `docker compose pull`
3. Recreate container: `docker compose up -d`
4. Nextcloud will handle database migrations automatically
5. Check logs for any issues

## Troubleshooting

### File Upload Issues
- Check PHP upload limits in Nextcloud config
- Verify disk space availability
- Check file permissions on data directory

### Performance Issues
- Add Redis/Memcached for caching
- Enable APCu for local caching
- Optimize database (especially for SQLite)

### Can't Access Web Interface
- Verify container is running
- Check port 4443 is not blocked
- Review container logs: `docker logs nextcloud`

## Notes

- Restart policy: `unless-stopped`
- Uses separate network (`homelab`) - ensure it exists
- Data directory should have sufficient storage space
- Consider regular backups of both config and data
- HTTPS is enabled by default on port 4443

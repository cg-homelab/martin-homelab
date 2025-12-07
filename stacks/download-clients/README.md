# Download Clients Stack

This stack provides VPN-protected download clients for torrents and usenet.

## Services

### delugevpn
- **Image**: `binhex/arch-delugevpn`
- **Purpose**: Torrent client with integrated VPN
- **Access**: `https://deluge.${DOMAIN_NAME}`
- **Ports**: 
  - `8112` - Web interface
  - `8118` - Privoxy HTTP proxy
  - `9118` - Socks proxy
  - `58846` - Deluge daemon
  - `58946` - Incoming connections (TCP/UDP)
- **Features**:
  - OpenVPN connection to PIA
  - Built-in Privoxy for HTTP proxy
  - SOCKS proxy support
  - Strict port forwarding

### nzbgetvpn
- **Image**: `jshridha/docker-nzbgetvpn:latest`
- **Purpose**: Usenet downloader with integrated VPN
- **Access**: `https://nzbget.${DOMAIN_NAME}`
- **Port**: `6789`
- **Features**:
  - OpenVPN connection to PIA
  - Built-in Privoxy
  - Efficient usenet downloading
  - RSS feed support

## Required Environment Variables

### Core Variables
- `DOMAIN_NAME`: Base domain for service access

### VPN Configuration (PIA)
- `PIA_USER`: Private Internet Access username
- `PIA_PASSWORD`: Private Internet Access password

### NZBGet
- `NZBGET_URI`: NZBGet web interface URI (optional)

### Optional Deluge Settings
- `LOG_LEVEL`: Logging verbosity (default: info)
- `LOG_HTML`: Log HTML content (default: false)
- `CAPTCHA_SOLVER`: CAPTCHA handling (default: none)
- `PORT`: Custom port mapping (default: 8191)

## Port Mappings

### Deluge
- `8112` - Web interface
- `8118` - Privoxy HTTP proxy
- `9118` - SOCKS proxy
- `58846` - Daemon port
- `58946` - Incoming connections (TCP and UDP)

### NZBGet
- `6789` - Web interface

## Volume Mounts

### Deluge
- `/mnt/array/downloads` - Download directory
- `/mnt/fast/appdata/delugevpn` - Configuration
- `/etc/localtime` - System time (read-only)

### NZBGet
- `/mnt/array/downloads` - Download directory
- `/mnt/fast/appdata/nzbgetvpn` - Configuration
- `/etc/localtime` - System time (read-only)

## VPN Configuration

Both services use:
- **Provider**: Private Internet Access (PIA)
- **Client**: OpenVPN
- **Features**:
  - Kill switch (VPN_ENABLED=yes)
  - Strict port forwarding (Deluge only)
  - LAN network access: `192.168.30.0/24`

### DNS Servers
Multiple DNS servers configured for redundancy:
- Cloudflare: 1.1.1.1, 1.0.0.1
- Quad9: 9.9.9.9
- Adguard: 94.140.14.14
- Others for fallback

## Network Configuration

- **LAN Network**: `192.168.30.0/24` (accessible even when VPN is active)
- **Backend Network**: For integration with Sonarr/Radarr
- Both services require `NET_ADMIN` capability for VPN functionality

## User/Group Configuration

- Root level: UID/GID 0 (for VPN)
- Process level: UID/GID 1000
- UMASK: 000 (permissive file permissions)

## Security Features

### VPN Kill Switch
If VPN disconnects:
- All internet traffic is blocked
- Only LAN access remains active
- Prevents IP leaks

### Proxy Services
Both clients provide proxy access:
- Privoxy (HTTP proxy)
- SOCKS proxy (Deluge only)
- Useful for routing other applications through VPN

## Accessing Download Clients

### From *arr Applications
Use the backend network:
- Deluge: `http://delugevpn:8112`
- NZBGet: `http://nzbgetvpn:6789`

### From Browser
- Deluge: `https://deluge.${DOMAIN_NAME}`
- NZBGet: `https://nzbget.${DOMAIN_NAME}`

## Configuration

### First-Time Setup

**Deluge:**
1. Access web interface
2. Default password: `deluge`
3. Change password immediately
4. Configure download paths
5. Enable labels and plugins as needed

**NZBGet:**
1. Access web interface
2. Default credentials: `nzbget` / `tegbzn6789`
3. Change credentials immediately
4. Add usenet server details
5. Configure categories

## Integration with Servarr

In Sonarr/Radarr/Readarr:
1. Add download client (Deluge or NZBGet)
2. Host: Service name (`delugevpn` or `nzbgetvpn`)
3. Port: `8112` (Deluge) or `6789` (NZBGet)
4. Configure categories/labels
5. Test connection

## Monitoring VPN Connection

Check logs to verify VPN connectivity:
```bash
docker logs delugevpn | grep -i "connected"
docker logs nzbgetvpn | grep -i "connected"
```

## Troubleshooting

### VPN Not Connecting
- Verify PIA credentials
- Check if PIA service is operational
- Review container logs for errors

### Downloads Not Working
- Verify VPN is connected
- Check port forwarding status
- Ensure downloads directory has proper permissions

### Can't Access Web Interface
- Verify container is running
- Check if VPN is connected
- Ensure Traefik labels are correct

## Notes

- Downloads directory must be accessible with proper permissions
- Both services must successfully connect to VPN before allowing traffic
- LAN access is always available even if VPN fails
- Consider using the same download directory for both clients
- PIA account required for both services

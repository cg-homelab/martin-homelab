# Tailscale Main Server Stack

This stack provides secure VPN mesh networking via Tailscale.

## Services

### tailscale
- **Image**: `tailscale/tailscale:latest`
- **Purpose**: Zero-config VPN mesh network
- **Features**:
  - Peer-to-peer encrypted connections
  - Easy access to homelab from anywhere
  - No port forwarding required
  - Built-in authentication via SSO
  - Cross-platform support

## Required Environment Variables

No explicit environment variables required in the compose file, but Tailscale needs initial authentication.

## Optional Environment Variables

- `TS_EXTRA_ARGS`: Additional Tailscale arguments (set to `--advertise-tags=tag:container`)
- `TS_STATE_DIR`: State directory (set to `/var/lib/tailscale`)
- `TS_SERVE_CONFIG`: Serve configuration file (set to `/config/ts-serve.json`)
- `TS_USERSPACE`: Whether to use userspace networking (set to `false` for better performance)
- `TS_AUTH_ONCE`: Authenticate only once (set to `true`)

## Volume Mounts

- `/mtn/fast/appdata/tailscale` - Tailscale state and configuration (Note: path has typo - should be `/mnt`)
- `${PWD}/config` - Additional configuration files

## Device Requirements

- `/dev/net/tun` - TUN device for VPN networking

## Capabilities

- `net_admin` - Required for network configuration

## Networks

- `homelab`: External network for service communication

## What is Tailscale?

Tailscale creates a secure network (tailnet) that:
- Connects all your devices via encrypted WireGuard tunnels
- Works across NAT and firewalls
- Provides stable IP addresses for each device
- Enables secure remote access
- No central VPN server needed (peer-to-peer)

## Initial Setup

### First-Time Authentication

1. Start the container:
   ```bash
   docker compose up -d
   ```

2. Get authentication URL:
   ```bash
   docker logs tailscale
   ```

3. Visit the URL to authenticate with your Tailscale account

4. Container will stay authenticated (TS_AUTH_ONCE=true)

### Tailscale Account

Need a Tailscale account:
- Free for personal use (up to 20 devices)
- Sign up at https://tailscale.com/
- Supports SSO (Google, Microsoft, GitHub, etc.)

## Configuration

### Tags

Configured with `--advertise-tags=tag:container`:
- Identifies this node as a container
- Useful for ACL rules
- Helps organize your tailnet

### Serve Configuration

Optional `ts-serve.json` configuration:
- Share services via Tailscale
- Expose local services to tailnet
- Configure HTTPS termination
- Set up funnel (public access)

Example `ts-serve.json`:
```json
{
  "TCP": {
    "443": {
      "HTTPS": true
    }
  },
  "Web": {
    "example.ts.net:443": {
      "Handlers": {
        "/": {
          "Proxy": "http://localhost:8080"
        }
      }
    }
  }
}
```

## Accessing Homelab via Tailscale

Once connected:
1. Install Tailscale on client devices
2. Connect to your tailnet
3. Access homelab services by Tailscale IPs
4. Or use MagicDNS for hostnames

### MagicDNS

Tailscale provides automatic DNS:
- Each device gets a name
- Example: `homelab-server.tailnet-name.ts.net`
- Access services via hostname instead of IP

## Security Features

### Encryption
- All traffic encrypted with WireGuard
- End-to-end encryption
- Perfect forward secrecy

### Authentication
- SSO integration
- Multi-factor authentication support
- Per-device authentication

### Access Control
Define ACL rules:
- Control which devices can access what
- Tag-based rules
- User-based rules
- Subnet routing rules

### Key Expiry
Keys can expire:
- Force re-authentication
- Revoke access for lost devices
- Configurable expiration period

## Use Cases

### Remote Access
- Access homelab from anywhere
- No need to expose ports to internet
- Secure alternative to VPN server

### Site-to-Site
- Connect multiple locations
- Share resources between sites
- No complex VPN setup

### Exit Node
Configure as exit node:
- Route all traffic through homelab
- Secure public WiFi usage
- Access geo-restricted content

### Subnet Routes
Advertise local subnets:
```bash
docker exec tailscale tailscale up --advertise-routes=192.168.1.0/24
```

Gives tailnet access to entire local network.

## Monitoring

### Check Status
```bash
docker exec tailscale tailscale status
```

Shows:
- Connected peers
- IP addresses
- Last seen times
- Connection status

### Network Info
```bash
docker exec tailscale tailscale netcheck
```

Shows:
- Connection latency
- Available DERP relays
- NAT type
- Connectivity

## Troubleshooting

### Can't Connect to Peers
- Check both devices are authenticated
- Verify network connectivity
- Check ACL rules
- Try `tailscale ping <peer>`

### Performance Issues
- Verify TUN device is available
- Check userspace networking is disabled (TS_USERSPACE=false)
- Review network capabilities
- Check for NAT issues

### Authentication Fails
- Verify Tailscale account is active
- Check authentication URL is valid
- Ensure device limit not reached
- Review container logs

## Container-Specific Considerations

### Persistent State
State stored in `/var/lib/tailscale`:
- Keep volume mounted for persistent connection
- Backup this directory to preserve authentication
- Don't share between containers

### Network Mode
Not using host network:
- Tailscale runs in container namespace
- Uses TUN device for VPN
- Requires net_admin capability

## Integration with Homelab

### Accessing Services
Once Tailscale is running:
1. Services remain accessible on local network
2. Also accessible via Tailscale IP
3. Can replace/supplement Traefik for external access
4. More secure than port forwarding

### With Traefik
Can use together:
- Traefik for local access
- Tailscale for remote access
- Or expose Traefik via Tailscale only

## Backup

Backup Tailscale state:
```bash
tar -czf tailscale-backup.tar.gz /mnt/fast/appdata/tailscale
```

Note: Path in compose has typo (`/mtn/` instead of `/mnt/`), verify correct path for your setup.

## Notes

- Restart policy: `unless-stopped`
- Requires TUN device access
- Needs net_admin capability
- Free tier: 20 devices, 100 users
- Paid tier: Unlimited devices, more features
- Uses WireGuard protocol
- Path typo in volume mount (mtn → mnt) - verify before use
- Network name: `homelab` (ensure it exists)
- TS_AUTH_ONCE prevents re-authentication on restart

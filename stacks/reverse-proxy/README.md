# Reverse Proxy Stack

This stack provides the main reverse proxy infrastructure using Traefik v3.

## Services

### traefik
- **Image**: `traefik:v3.3.3`
- **Purpose**: Acts as the main reverse proxy and load balancer for the entire homelab
- **Features**:
  - Automatic HTTPS with Let's Encrypt
  - Cloudflare DNS challenge for certificate generation
  - Dashboard for monitoring routes and services
  - Integration with Docker for automatic service discovery

## Required Environment Variables

### Core Variables
- `DOMAIN_NAME`: Base domain for all services (e.g., `example.com`)
- `TRAEFIK_DASHBOARD_CREDENTIALS`: Basic auth credentials for Traefik dashboard (format: `user:hashedpassword`)
  - Generate with: `htpasswd -nb username password` (note: escape `$` as `$$` in compose)

### Cloudflare API (for DNS challenge)
- Requires a secret file at: `/mnt/fast/appdata/traefik/cf_api_token.txt`
  - Contains your Cloudflare API token with DNS edit permissions

## Required Files

Before starting, manually create these files:
1. `/mnt/fast/appdata/traefik/data/traefik.yml` - Main Traefik configuration
2. `/mnt/fast/appdata/traefik/data/acme.json` - Certificate storage (set permissions to 600)
3. `/mnt/fast/appdata/traefik/data/config.yml` - Additional configuration
4. `/mnt/fast/appdata/traefik/cf_api_token.txt` - Cloudflare API token

## Ports

- `180:80` - HTTP entrypoint
- `1443:443` - HTTPS entrypoint (TCP and UDP for HTTP/3)
- `8080:8080` - Traefik dashboard

## Networks

- `frontend`: External network for services exposed via Traefik
- `backend`: External network for backend service communication

## Configuration

The Traefik dashboard is accessible at: `https://traefik.${DOMAIN_NAME}`

## Notes

- Ensure Docker networks `frontend` and `backend` exist before starting
- The dashboard requires authentication using the credentials specified in `TRAEFIK_DASHBOARD_CREDENTIALS`
- All services that need HTTPS should connect to the `frontend` network and use Traefik labels

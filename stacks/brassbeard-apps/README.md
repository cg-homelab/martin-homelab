# Brassbeard Apps Stack

This stack provides utility applications.

## Services

### chmod-calculator (app)
- **Image**: `brassbeard/chmod-calculator:latest`
- **Purpose**: Web-based Unix file permission calculator
- **Access**: `https://chmod.clenchedgaming.com`
- **Features**:
  - Visual chmod permission calculator
  - Convert between numeric and symbolic notation
  - Educational tool for understanding Unix permissions
  - Simple, lightweight interface

## Required Environment Variables

No environment variables required.

## Port Mappings

Internal port 80 is used but not published to host. Access is via Traefik only.

## Networks

- `frontend`: For Traefik access

## What is a Chmod Calculator?

A chmod calculator helps work with Unix file permissions:

### Numeric Notation (Octal)
- Read (r) = 4
- Write (w) = 2
- Execute (x) = 1

Combined for owner, group, and others:
- `755` = rwxr-xr-x (owner: full, group: read+execute, others: read+execute)
- `644` = rw-r--r-- (owner: read+write, group: read, others: read)

### Symbolic Notation
- `u` = user/owner
- `g` = group
- `o` = others
- `a` = all

Examples:
- `chmod u+x file` - Add execute for owner
- `chmod go-w file` - Remove write for group and others

## Features

### Interactive Permissions
- Click checkboxes to select permissions
- Instantly see numeric and symbolic equivalents
- Visual representation of rwx permissions
- Separate controls for owner, group, others

### Common Permissions
Quick select common permission sets:
- `644` - Standard file (rw-r--r--)
- `755` - Executable file (rwxr-xr-x)
- `777` - Full permissions (rwxrwxrwx)
- `600` - Private file (rw-------)
- `700` - Private executable (rwx------)

### Educational
Helps understand:
- How numeric permissions work
- Relationship between bits and permissions
- Impact of different permission combinations
- Security implications of permissions

## Accessing the Tool

Via Traefik:
- URL: `https://chmod.clenchedgaming.com`
- Accessible over HTTPS only
- No authentication required (public tool)

## Use Cases

### For System Administrators
- Quickly calculate permissions for scripts
- Verify correct permissions for services
- Teach others about Unix permissions
- Document permission requirements

### For Developers
- Set correct permissions for application files
- Understand permission errors
- Configure deployment scripts
- Security audits

### For Learning
- Interactive way to learn Unix permissions
- Visual feedback helps understanding
- Practice permission calculations
- Safe environment to experiment

## Security Notes

- Public-facing utility application
- No sensitive data processed
- Read-only educational tool
- No file system access
- Static web application

## Traefik Configuration

Configured with:
- HTTPS enabled via Traefik
- Specific domain: `chmod.clenchedgaming.com`
- Websecure entrypoint (HTTPS)
- TLS enabled

## Alternative Access

If you need to access without the specific domain:
1. Modify Traefik labels to use `${DOMAIN_NAME}`
2. Or access via different domain
3. Or add port mapping for direct access

Current configuration uses hardcoded domain.

## Similar Tools

Other permission calculators:
- Online: chmod-calculator.com
- Command line: `stat -c %a file`
- Built into file managers

This containerized version:
- Available offline
- Part of your homelab
- No external dependencies
- Privacy-focused (no tracking)

## Technical Details

### Image
- Based on web server (likely nginx or similar)
- Serves static HTML/CSS/JavaScript
- Lightweight container
- Minimal resource usage

### Network
- Only connected to frontend network
- Accessed via Traefik reverse proxy
- No direct host port exposure
- HTTPS only

## Maintenance

### Updates
Check for image updates:
```bash
docker compose pull
docker compose up -d
```

### Logs
View access logs:
```bash
docker logs chmod-calculator
```

## Troubleshooting

### Can't Access Tool
- Verify domain resolves to your server
- Check Traefik is running
- Review Traefik labels configuration
- Verify TLS certificate is valid

### Wrong Domain
If you need different domain:
```yaml
labels:
  - "traefik.http.routers.chmod.rule=Host(`chmod.${DOMAIN_NAME}`)"
  - "traefik.http.routers.chmod-secure.rule=Host(`chmod.${DOMAIN_NAME}`)"
```

## Notes

- Restart policy: `unless-stopped`
- Minimal resource usage
- Useful utility for system administration
- Educational tool
- No data persistence needed
- Hardcoded domain in current configuration
- Consider updating to use `${DOMAIN_NAME}` for flexibility

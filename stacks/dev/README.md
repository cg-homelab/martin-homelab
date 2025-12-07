# Development Stack

This stack provides development tools accessible via web browser.

## Services

### code-server
- **Image**: `linuxserver/code-server:latest`
- **Purpose**: VS Code running in the browser
- **Access**: `https://code.${DOMAIN_NAME}`
- **Port**: `8443`
- **Features**:
  - Full VS Code experience in browser
  - Extension support
  - Terminal access
  - Remote file editing

## Required Environment Variables

- `DOMAIN_NAME`: Base domain for service access
- `PASSWORD`: Login password for code-server
- `SUDO_PASSWORD`: Password for sudo operations within code-server

## Optional Environment Variables

- `PROXY_DOMAIN`: Custom domain for code-server (defaults to routing via Traefik)
- `DEFAULT_WORKSPACE`: Default workspace path (set to `/config/workspace`)
- `PWA_APPNAME`: Progressive Web App name (default: `code-server`)

## Port Mappings

- `8443` - Code-server web interface

## Volume Mounts

- `/mnt/fast/appdata/code-server/config` - Code-server configuration and settings
- `/mnt/fast/appdata` - Mounted for editing homelab configuration files remotely

## User/Group Configuration

- `PUID=1000`
- `PGID=1000`
- `TZ=Etc/UTC`

## Networks

- `backend`: For internal access
- `frontend`: For Traefik access

## Features

### VS Code in Browser
Full Visual Studio Code functionality:
- Syntax highlighting
- IntelliSense
- Git integration
- Debugging
- Extensions marketplace

### Terminal Access
Integrated terminal with:
- Full shell access
- Command-line tools
- Package installation (with sudo)

### Remote File Editing
- Edit homelab configuration files remotely
- Direct access to `/mnt/fast/appdata`
- Make changes without SSH

## Security Notes

- Set a strong `PASSWORD` for login
- Set a strong `SUDO_PASSWORD` for elevated operations
- Access is protected by Traefik (HTTPS)
- Consider additional authentication via Traefik middleware

## Usage

### First Access
1. Navigate to `https://code.${DOMAIN_NAME}`
2. Enter password set in `PASSWORD` environment variable
3. Start coding!

### Installing Extensions
Extensions can be installed from the marketplace like regular VS Code.

### Using Terminal
1. Open terminal in code-server (Ctrl+` or Terminal menu)
2. Use sudo with `SUDO_PASSWORD` for elevated commands

### Editing Configuration Files
1. Open file browser
2. Navigate to `/appdata/` (mapped to `/mnt/fast/appdata`)
3. Edit configuration files for other services
4. Save changes directly

## Common Use Cases

- Edit Docker Compose files
- Modify service configurations
- Write automation scripts
- Debug issues with syntax highlighting
- Access multiple files across services

## Workspace Configuration

Default workspace: `/config/workspace`
- Persisted across container restarts
- Store projects and settings here
- Git repositories can be cloned here

## Notes

- Restart policy: `unless-stopped`
- Full access to appdata allows editing all service configurations
- Terminal has access to standard Linux tools
- Sudo access allows installing additional packages
- Consider the security implications of password-only authentication

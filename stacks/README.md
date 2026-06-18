# Docker compose stacks

## TrueNas

If you want to the native truenas apps feature

### Setup

- Create datasets
  - apps/
    - apps/homelab/
    - apps/appconfig/
    - apps/databases/
  - media/
    - media/movies/
    - media/series/
- In CLI go to parent directory of apps `sudo chown -R 1000:1000 apps`
- In CLI go to parent directory of media `sudo chown -R 1000:1000 media`

### Get git files

```bash
# Getting repo
cd /mnt/pond/apps
git clone https://github.com/cg-homelab/martin-homelab.git homelab

# Setup initial global env files
cd homelab/stacks
cp example.global.env global.env
```

### Before adding stack app

1. Create dataset for app
   In truenas create dataset under apps/appconfig/{{STACKNAME}}

2. Prepare app files

```bash

cd /mnt/pond/apps/appconfig
sudo chown 1000:1000 {{STACKNAME}}
cd /mnt/pond/apps/homelab/stacks/{{STACKNAME}}
cp example.env .env
```

3. Edit .env file

4. Create app in truenas ui by selecting:
   Discover -> ... -> install via yaml -> paste this:

```yaml
include:
  - path: /mnt/pond/apps/homelab/stacks/{{STACKNAME}}/compose.yaml
  - env_file: /mnt/pond/apps/homelab/stacks/global.env
services: {}
```

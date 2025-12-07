# Semaphore UI Stack

This stack provides a web-based UI for running Ansible playbooks.

## Services

### semaphore
- **Image**: `semaphoreui/semaphore:v2.12.14`
- **Purpose**: Modern web UI for Ansible automation
- **Port**: `3005` (host) → `3000` (container)
- **Features**:
  - Web-based Ansible playbook execution
  - Project and inventory management
  - Task scheduling and history
  - User and team management
  - Secrets management

## Required Environment Variables

- `ADMIN_PASSWORD`: Password for the admin user
- `ADMIN_NAME`: Admin username
- `ADMIN_EMAIL`: Admin email address
- `ADMIN`: Admin username (appears to be duplicate of ADMIN_NAME)
- `ACCESS_KEY`: Encryption key for sensitive data

## Optional Environment Variables

- `SEMAPHORE_DB_DIALECT`: Database type (set to `bolt` for embedded database)
- `ANSIBLE_HOST_KEY_CHECKING`: Whether to check SSH host keys (set to `false`)

## Port Mappings

- `3005` - Semaphore web interface

## Volume Mounts

- `semaphore_data` - Application data and BoltDB database
- `semaphore_config` - Configuration files
- `semaphore_tmp` - Temporary files for playbook execution
- `/mnt/fast/appdata/semaphoreui` - Additional storage mounted to container

## Networks

- `frontend`: For Traefik access
- `backend`: For internal communication

## Database

Uses BoltDB (embedded database):
- No external database required
- File-based storage
- Automatic backups recommended
- Stored in `semaphore_data` volume

## Initial Setup

1. Access Semaphore at `http://<server-ip>:3005`
2. Login with credentials:
   - Username: Value from `ADMIN_NAME`
   - Password: Value from `ADMIN_PASSWORD`
3. Complete initial project setup

## Configuration

### Projects
1. Create a new project
2. Add SSH keys for accessing target hosts
3. Define repositories (Git URLs with playbooks)
4. Configure inventory sources

### Inventory
Define your infrastructure:
- Static inventory (manual host lists)
- Dynamic inventory (scripts)
- Import from files

### Repositories
Link to Git repositories containing:
- Ansible playbooks
- Roles
- Variables
- Inventory files

### Task Templates
Create reusable task templates:
1. Select playbook from repository
2. Define inventory
3. Configure environment variables
4. Set credentials
5. Add optional survey questions

## Security

### SSH Keys
- Store SSH private keys in Semaphore
- Used for connecting to managed hosts
- Encrypted using `ACCESS_KEY`

### Ansible Vault
- Support for Ansible Vault passwords
- Encrypted storage of secrets
- Decrypt during playbook execution

### Access Control
- User roles (Admin, Task Runner, Guest)
- Project-level permissions
- Task execution approval workflows

## Running Playbooks

### Manual Execution
1. Go to Task Templates
2. Select template
3. Click "Run"
4. Monitor execution in real-time
5. View logs and results

### Scheduled Execution
1. Edit task template
2. Add cron schedule
3. Playbook runs automatically

### Webhooks
Trigger playbooks via HTTP webhooks:
- Get webhook URL from task template
- Send POST request to trigger
- Useful for CI/CD integration

## Integration with Homelab

### Using Homelab Ansible Playbooks

Mount your ansible directory:
```yaml
volumes:
  - /home/runner/work/martin-homelab/martin-homelab/ansible:/ansible
```

Or use Git repository:
1. Add repository in Semaphore
2. Point to this repository
3. Path: `/ansible/ubuntu`
4. Access playbooks directly

### Example: System Updates
1. Create inventory with your Ubuntu servers
2. Create repository pointing to this repo
3. Create task template:
   - Playbook: `ansible/ubuntu/upd-apt.yaml`
   - Inventory: Your servers
   - Run on schedule or manually

## Ansible Configuration

### Host Key Checking
Disabled by default (`ANSIBLE_HOST_KEY_CHECKING=false`):
- Simplifies initial setup
- Consider enabling for production
- Add host keys to known_hosts

### Extra Variables
Pass variables to playbooks:
- In task template configuration
- From survey questions (user prompts)
- From environment settings

## Logging and History

- All task executions are logged
- View historical runs
- Filter by status, project, user
- Download execution logs

## Backup

Important data to backup:
```bash
# Backup Semaphore data (includes BoltDB)
docker run --rm -v semaphoreui_semaphore_data:/data \
  -v $(pwd):/backup alpine \
  tar czf /backup/semaphore-data.tar.gz /data

# Backup configuration
docker run --rm -v semaphoreui_semaphore_config:/data \
  -v $(pwd):/backup alpine \
  tar czf /backup/semaphore-config.tar.gz /data
```

## Troubleshooting

### Can't Access Web Interface
- Verify container is running
- Check port 3005 is available
- Review container logs

### Playbook Execution Fails
- Check SSH keys are correctly configured
- Verify inventory hosts are accessible
- Review Ansible output logs in Semaphore
- Check file permissions on mounted volumes

### Database Errors
- Ensure `semaphore_data` volume has proper permissions
- Check disk space availability
- Review application logs

## Notes

- Restart policy: `unless-stopped`
- BoltDB is suitable for small to medium deployments
- For larger deployments, consider PostgreSQL or MySQL
- SSH host key checking is disabled (adjust for production)
- `ACCESS_KEY` must be kept secure - used for encryption
- Mounted `/mnt/fast/appdata/semaphoreui` can store playbooks or logs

## Related Playbooks

This repository includes Ansible playbooks in `ansible/ubuntu/`:
- `upd-apt.yaml` - System updates
- `maint-reboot.yaml` - Reboot management
- `maint-reboot-required.yaml` - Check if reboot needed
- `maint-disk-space.yaml` - Disk space monitoring
- `setup-add-ssh-key.yaml` - SSH key management

These can be managed through Semaphore UI.

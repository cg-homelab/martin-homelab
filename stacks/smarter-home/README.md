# Smarter Home Stack

This stack provides a custom home automation backend API.

## Services

### sm-api
- **Image**: `zevome/smarter-home-api:latest`
- **Purpose**: Custom home automation backend service
- **Access**: `https://sm-api.${DOMAIN_NAME}`
- **Port**: Configurable via `BACKEND_PORT` environment variable
- **Features**:
  - RESTful API for home automation
  - Database-backed storage
  - JWT-based authentication
  - Configurable logging

## Required Environment Variables

### Core Configuration
- `DOMAIN_NAME`: Base domain for service access
- `BACKEND_PORT`: Port for the API service to listen on (internal to container)
- `FRONTEND_PORT`: Port for frontend application (for CORS configuration)

### Database
- `DATABASE_URL`: PostgreSQL connection string
  - Format: `postgresql://user:password@host:port/database`
  - Example: `postgresql://user:pass@db-pg:5432/smarterhome`

### Security
- `AUTH_SECRET`: Secret key for authentication
- `JWT_SECRET`: Secret key for JWT token signing (uses same value as AUTH_SECRET)

### Logging
- `LOG_LEVEL`: Application log level (e.g., `info`, `debug`, `warn`, `error`)

## Port Mappings

- `${BACKEND_PORT}:3001` - API endpoint (configurable host port, container always uses 3001)

## Networks

- `frontend`: For Traefik access
- `backend`: For database access

## Database Setup

This service requires a PostgreSQL database:

### Using the Databases Stack
1. Ensure `databases` stack is running
2. Create a database:
   ```bash
   docker exec db-pg psql -U postgres -c "CREATE DATABASE smarterhome;"
   ```
3. Set `DATABASE_URL`:
   ```
   DATABASE_URL=postgresql://postgres:${DB_PASSWORD}@db-pg:5432/smarterhome
   ```

### Using External Database
Point `DATABASE_URL` to your external PostgreSQL instance.

## API Endpoints

The API provides endpoints for home automation control. Exact endpoints depend on the application implementation. Typically includes:
- Device management
- Automation rules
- Scenes and routines
- Status monitoring
- User management

Refer to the API documentation or source code for specific endpoints.

## Authentication

Uses JWT-based authentication:
1. Login endpoint provides JWT token
2. Include token in `Authorization: Bearer <token>` header
3. Token expires based on configuration
4. Refresh tokens may be available

## Security Configuration

### Secrets
Both `AUTH_SECRET` and `JWT_SECRET` should be:
- Long random strings (32+ characters)
- Kept secure and not committed to version control
- Rotated periodically

### Generate Secrets
```bash
# Generate a secure random secret
openssl rand -base64 32
```

## CORS Configuration

Frontend port is configured via `FRONTEND_PORT`:
- Used for CORS (Cross-Origin Resource Sharing)
- Allows frontend application to call API
- Set to the port where your frontend is hosted

## Logging

Configurable via `LOG_LEVEL`:
- `error`: Only errors
- `warn`: Warnings and errors
- `info`: General information (recommended)
- `debug`: Detailed debugging information
- `trace`: Very verbose logging

## Integration with Frontend

The API is designed to work with a frontend application:
1. Frontend makes HTTP requests to API
2. API processes requests and returns JSON
3. JWT tokens used for authentication
4. CORS configured based on `FRONTEND_PORT`

## Example Environment Variables

```env
DOMAIN_NAME=example.com
BACKEND_PORT=3002
FRONTEND_PORT=3000
DATABASE_URL=postgresql://postgres:password@db-pg:5432/smarterhome
AUTH_SECRET=your-very-secure-random-secret-here
LOG_LEVEL=info
```

## Development

For development:
1. Set `LOG_LEVEL=debug` for verbose logging
2. Monitor logs: `docker logs sm-api -f`
3. Use API testing tools (Postman, curl, etc.)
4. Check database connections

## Backup

### Database Backup
```bash
# Backup database
docker exec db-pg pg_dump -U postgres smarterhome > smarterhome-backup.sql
```

### Configuration Backup
Environment variables and secrets should be securely backed up.

## Monitoring

Monitor the service:
```bash
# View logs
docker logs sm-api

# Follow logs
docker logs sm-api -f

# Check container status
docker ps | grep sm-api
```

## Troubleshooting

### Can't Connect to Database
- Verify `DATABASE_URL` is correct
- Check database exists
- Ensure database stack is running
- Verify network connectivity

### Authentication Issues
- Check `AUTH_SECRET` and `JWT_SECRET` are set
- Verify secrets match between container restarts
- Review API logs for authentication errors

### CORS Errors
- Verify `FRONTEND_PORT` matches frontend configuration
- Check browser console for CORS errors
- May need to configure additional CORS settings

### API Not Accessible
- Verify container is running
- Check Traefik labels are correct
- Ensure domain resolves correctly
- Review Traefik logs

## Notes

- Restart policy: `unless-stopped`
- Internal API always runs on port 3001
- External port is configurable via `BACKEND_PORT`
- Requires PostgreSQL database
- JWT tokens used for stateless authentication
- Custom application - refer to source code for detailed API documentation

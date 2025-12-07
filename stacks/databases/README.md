# Databases Stack

This stack provides PostgreSQL-based database services.

## Services

### db-pg
- **Image**: `postgres`
- **Purpose**: General-purpose PostgreSQL database server
- **Port**: `5432`
- **Features**:
  - Standard PostgreSQL instance
  - 2GB shared memory
  - Persistent data storage

### db-ts
- **Image**: `timescale/timescaledb-ha:pg17`
- **Purpose**: TimescaleDB for time-series data
- **Features**:
  - PostgreSQL 17 with TimescaleDB extension
  - High-availability image
  - Optimized for time-series workloads
  - 2GB shared memory
- **Note**: Exposed only on backend network (not published to host)

### db-adminer
- **Image**: `adminer`
- **Purpose**: Lightweight database management interface
- **Access**: `https://adminer.${DOMAIN_NAME}`
- **Port**: `3080:8080`
- **Features**:
  - Web-based database administration
  - Support for multiple database types
  - Simple and lightweight

## Required Environment Variables

- `DOMAIN_NAME`: Base domain for Adminer access
- `DB_USERNAME`: Username for TimescaleDB (default database user)
- `DB_PASSWORD`: Password for both PostgreSQL and TimescaleDB

## Port Mappings

- `5432` - PostgreSQL (db-pg) - Published to host
- `3080` - Adminer web interface - Published to host
- TimescaleDB only accessible via backend network

## Volume Mounts

- `/mnt/fast/databases/postgres` - PostgreSQL data directory
- `/mnt/fast/databases/timescale` - TimescaleDB data directory

## Networks

- `backend`: External network for database access

## Database Configuration

### PostgreSQL (db-pg)
- Data directory: `/var/lib/postgresql/data/pgdata`
- Shared memory: 2048MB
- Password set via `DB_PASSWORD`

### TimescaleDB (db-ts)
- Default database: `defaultdb`
- Username: Set via `DB_USERNAME`
- Password: Set via `DB_PASSWORD`
- Shared memory: 2048MB

## Accessing Databases

### Via Adminer
1. Navigate to `https://adminer.${DOMAIN_NAME}`
2. Select database system (PostgreSQL)
3. Enter connection details:
   - Server: `db-pg` or `db-ts`
   - Username: `postgres` (db-pg) or `${DB_USERNAME}` (db-ts)
   - Password: `${DB_PASSWORD}`
   - Database: (leave empty for default)

### Via Application
Connect from other containers on the backend network:
- PostgreSQL: `postgresql://postgres:${DB_PASSWORD}@db-pg:5432/database_name`
- TimescaleDB: `postgresql://${DB_USERNAME}:${DB_PASSWORD}@db-ts:5432/defaultdb`

### Via Host
- PostgreSQL: `localhost:5432`

## Backup Recommendations

Regular backups are recommended:
```bash
# PostgreSQL backup
docker exec db-pg pg_dump -U postgres database_name > backup.sql

# TimescaleDB backup
docker exec db-ts pg_dump -U ${DB_USERNAME} defaultdb > backup.sql
```

## Performance Notes

- Shared memory is set to 2GB for both databases
- Adjust based on available system resources
- TimescaleDB is optimized for time-series data with automatic chunking

## Security Notes

- Change `DB_PASSWORD` from default value
- Consider using Docker secrets for sensitive credentials
- Adminer should be protected behind authentication
- TimescaleDB is not exposed to host network for security

## Notes

- Ensure `/mnt/fast/databases/` directory exists with proper permissions
- Both databases use `unless-stopped` restart policy
- Backend network must exist before starting

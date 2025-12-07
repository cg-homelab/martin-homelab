# SurrealDB Stack

This stack provides a distributed SurrealDB database with TiKV backend for high availability and scalability.

## Services

### Placement Driver (PD) Cluster
Three PD instances providing cluster coordination:
- **pd0, pd1, pd2**
- **Image**: `pingcap/pd:v8.5.0`
- **Purpose**: Manages TiKV cluster metadata and scheduling
- **Ports**: `2379` (client), `2380` (peer communication)

### TiKV Storage Cluster
Three TiKV instances providing distributed storage:
- **tikv0, tikv1, tikv2**
- **Image**: `pingcap/tikv:v8.5.0`
- **Purpose**: Distributed key-value storage layer
- **Ports**: `20160` (service), `20180` (status)

### SurrealDB
- **Image**: `surrealdb/surrealdb:latest`
- **Purpose**: Multi-model database built on TiKV
- **Access**: `https://surrealdb.${DOMAIN_NAME}`
- **Port**: `8000`
- **Features**:
  - SQL-like query language (SurrealQL)
  - Document, graph, and relational models
  - Real-time subscriptions
  - Built-in authentication

## Required Environment Variables

- `DOMAIN_NAME`: Base domain for service access
- `DB_USER`: Root username for SurrealDB
- `DB_PASSWORD`: Root password for SurrealDB

## Architecture

### High Availability Setup
- **3x PD nodes**: Quorum-based consensus (can tolerate 1 failure)
- **3x TiKV nodes**: Distributed storage with replication
- **1x SurrealDB**: Database server (can be scaled horizontally)

### Data Distribution
- PD manages data placement and cluster scheduling
- TiKV stores data across three nodes with automatic replication
- SurrealDB connects to TiKV cluster via PD endpoints

## Port Mappings

- `8000` - SurrealDB HTTP/WebSocket API

Internal ports (not exposed to host):
- `2379` - PD client communication
- `2380` - PD peer communication
- `20160` - TiKV service port
- `20180` - TiKV status port

## Volume Mounts

### Data Storage
- `/mnt/fast/databases/surreal/data` - All data for PD and TiKV nodes
  - `/data/pd0`, `/data/pd1`, `/data/pd2` - PD data
  - `/data/tikv0`, `/data/tikv1`, `/data/tikv2` - TiKV data

### Logs
- `/mnt/fast/databases/surreal/logs` - All log files
  - `/logs/pd0.log`, `/logs/pd1.log`, `/logs/pd2.log` - PD logs
  - `/logs/tikv0.log`, `/logs/tikv1.log`, `/logs/tikv2.log` - TiKV logs

## Networks

- `internal`: Private network for PD and TiKV communication
- `backend`: External network for SurrealDB access
- `frontend`: For Traefik access

## Health Checks

### PD Health Check
Uses `pd-ctl` to verify each PD node:
- Start period: 5 seconds
- Timeout: 10 seconds
- Retries: 5

### TiKV Health Check
Uses `tikv-ctl` to verify each TiKV node:
- Start period: 5 seconds
- Timeout: 10 seconds
- Retries: 5

## Startup Sequence

1. **PD Cluster**: All three PD nodes start and form cluster
2. **TiKV Cluster**: Once PD is healthy, TiKV nodes start and register
3. **SurrealDB**: Once TiKV is healthy, SurrealDB connects

## SurrealDB Configuration

### Connection String
SurrealDB connects to TiKV via PD:
```
tikv://pd0:2379
```

### Authentication
- Root user: `${DB_USER}`
- Root password: `${DB_PASSWORD}`
- Required for all database operations

### Logging
- Log level: `trace` (very verbose)
- Useful for debugging
- Consider reducing to `info` for production

## Accessing SurrealDB

### HTTP API
```bash
# Health check
curl http://localhost:8000/health

# Run query
curl -X POST http://localhost:8000/sql \
  -H "Content-Type: application/json" \
  -u ${DB_USER}:${DB_PASSWORD} \
  -d '{"query": "SELECT * FROM users"}'
```

### WebSocket API
Connect to `ws://localhost:8000/rpc` for real-time features.

### CLI Access
```bash
# Using Docker
docker exec -it surrealdb /surreal sql \
  --endpoint http://localhost:8000 \
  --username ${DB_USER} \
  --password ${DB_PASSWORD}
```

### From Applications
Connection string:
```
http://surrealdb:8000
# or
https://surrealdb.${DOMAIN_NAME}
```

## SurrealQL Examples

```sql
-- Create namespace and database
USE NS my_namespace DB my_database;

-- Create table
CREATE users SET name = "John", age = 30;

-- Query data
SELECT * FROM users;

-- Update data
UPDATE users:john SET age = 31;

-- Delete data
DELETE users:john;
```

## Scaling Considerations

### Current Configuration
- 3-node cluster (recommended minimum)
- Tolerates 1 node failure
- Data replicated across nodes

### Scaling Up
To add more TiKV nodes:
1. Add new tikv service to compose
2. Point to same PD cluster
3. PD will automatically rebalance data

### Scaling SurrealDB
Multiple SurrealDB instances can connect to same TiKV cluster:
```yaml
surrealdb-2:
  image: surrealdb/surrealdb:latest
  command:
    - start
    - --user=${DB_USER}
    - --pass=${DB_PASSWORD}
    - tikv://pd0:2379
```

## Performance

### PD Configuration
- WiredTiger cache limited to 256MB per node
- Optimized for metadata operations
- Low resource requirements

### TiKV Configuration
- Default settings suitable for most workloads
- Adjust based on available system resources
- Monitor status endpoints for metrics

## Monitoring

### PD Status
```bash
# Check PD cluster health
docker exec pd0 /pd-ctl health
```

### TiKV Status
```bash
# Check TiKV metrics
docker exec tikv0 /tikv-ctl --host tikv0:20160 metrics
```

### SurrealDB Logs
```bash
docker logs surrealdb
```

## Backup

### Data Backup
Important data locations:
- `/mnt/fast/databases/surreal/data` - All database data
- Backup should be done when cluster is stopped or use TiKV backup tools

```bash
# Stop cluster
docker compose down

# Backup data
tar -czf surrealdb-backup.tar.gz /mnt/fast/databases/surreal/

# Restart cluster
docker compose up -d
```

### For Online Backup
Use TiKV's built-in backup and restore tools (BR).

## Troubleshooting

### PD Cluster Won't Form
- Check all PD nodes are starting
- Verify initial-cluster configuration
- Review PD logs
- Ensure network connectivity

### TiKV Won't Join Cluster
- Verify PD cluster is healthy
- Check TiKV can reach PD endpoints
- Review TiKV logs
- Ensure data directories are writable

### SurrealDB Connection Issues
- Verify TiKV cluster is healthy
- Check PD endpoints are accessible
- Review SurrealDB logs
- Verify credentials are correct

## Security Considerations

- Change default `DB_USER` and `DB_PASSWORD`
- SurrealDB exposed via Traefik (HTTPS)
- Consider additional authentication middleware
- PD and TiKV are internal only (not exposed)
- Secure the data directory with proper permissions

## Notes

- Restart policy: `on-failure` (PD, TiKV), `always` (SurrealDB)
- Minimum 3 nodes required for HA
- Significant disk I/O requirements
- Monitor disk space closely
- Consider SSD storage for better performance
- Complex setup - ensure you need distributed database
- For simple use cases, consider PostgreSQL instead

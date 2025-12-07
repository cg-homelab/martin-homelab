# Grid Compute Stack

This stack contributes spare computing resources to distributed computing projects.

## Services

### foldingathome
- **Image**: `linuxserver/foldingathome:latest`
- **Purpose**: Contribute GPU/CPU power to protein folding research
- **Port**: `7396` (web interface, optional)
- **Features**:
  - NVIDIA GPU-accelerated folding
  - Helps medical research (diseases, drug design)
  - Earn points for contributions
  - Team support

## Required Environment Variables

- `FAH_TOKEN`: Folding@home account token
  - Get from https://foldingathome.org/
- `NAME`: Machine name for identification

## Optional Environment Variables

- `CLI_ARGS`: Additional command-line arguments for Folding@home client

## Port Mappings

- `7396` - Web interface (optional)

## Volume Mounts

- `/opt/appdata/foldingathome` - Configuration and work units

## GPU Requirements

### NVIDIA GPU
Required components:
- NVIDIA GPU with CUDA support
- NVIDIA drivers installed on host
- NVIDIA Container Toolkit configured
- Docker runtime: `nvidia`

### GPU Capabilities
Configured for:
- `gpu`: GPU computing
- `compute`: Compute operations

All available GPUs are allocated to the container.

## User/Group Configuration

- `PUID=1000`
- `PGID=1000`
- `TZ=Europe/Oslo`

## What is Folding@home?

Folding@home is a distributed computing project that:
- Simulates protein folding and dynamics
- Helps understand diseases (Alzheimer's, COVID-19, cancer, etc.)
- Aids in drug discovery
- Run by research institutions
- Uses volunteer computing power

## Setup

### Getting an Account Token

1. Visit https://foldingathome.org/
2. Create an account or log in
3. Get your account token from settings
4. Set as `FAH_TOKEN` environment variable

### Machine Name

Set a unique name for your machine:
- Helps identify contributions
- Shown on statistics pages
- Can be anything descriptive

### Joining a Team (Optional)

Optionally join a team to compete:
- Add team number via web interface
- Compete on leaderboards
- Coordinate with team members

## Web Interface

Access at `http://<server-ip>:7396`:
- View current work units
- Monitor GPU/CPU usage
- Configure folding settings
- Adjust power usage
- Pause/resume folding

## Power Settings

Configure how much computing power to use:
- **Light**: Minimal resource usage
- **Medium**: Moderate resource usage
- **Full**: Maximum resource usage (default)

Adjust based on:
- Available system resources
- Other running services
- Desired contribution level

## Folding While Idle

Can be configured to:
- Only fold when system is idle
- Pause when other applications need resources
- Resume automatically when idle

## GPU vs CPU Folding

### GPU Folding (Current Setup)
- Much faster than CPU
- More points per day
- Higher power consumption
- Better for modern GPUs

### CPU Folding
Can be enabled via CLI_ARGS or web interface:
- Slower than GPU
- Uses available CPU cores
- Less power efficient
- Useful if GPU is busy

## Statistics and Points

Track your contributions:
- Points earned per work unit
- Daily/monthly/total statistics
- Team rankings
- Individual rankings

View stats at: https://stats.foldingathome.org/

## Work Units

The client:
- Downloads work units from servers
- Processes simulations locally
- Uploads results when complete
- Automatically gets new work

Work unit types vary:
- Different proteins
- Different projects
- Different points values
- Different time requirements

## Resource Management

### With Other Services

If running other GPU services (Plex, Jellyfin, etc.):
- Folding@home can share GPU
- May impact other services' performance
- Consider adjusting power settings
- Monitor GPU usage

### Power Consumption

GPU folding increases:
- Power draw significantly
- Heat generation
- Fan noise

Monitor system temperatures and power usage.

## Monitoring

### Via Web Interface
Check `http://<server-ip>:7396` for:
- Current work unit progress
- GPU temperature and usage
- Points per day estimate
- Recent completions

### Via Logs
```bash
docker logs foldingathome
```

Shows:
- Work unit downloads
- Completion status
- Errors or issues
- Performance metrics

## Pausing Folding

### Temporary Pause
Via web interface or:
```bash
docker pause foldingathome
```

### Stop Container
```bash
docker compose down
```

Work in progress is saved and resumed when restarted.

## Troubleshooting

### GPU Not Detected
- Verify NVIDIA drivers are installed
- Check nvidia-docker runtime is available
- Ensure GPU is not in use exclusively by another process
- Review container logs for errors

### No Work Units Available
- Folding@home servers may be busy
- Try different project preferences
- Check network connectivity
- Wait and retry (usually temporary)

### Low Performance
- Check GPU is not throttling due to temperature
- Verify other GPU-using containers aren't competing
- Adjust power settings
- Check available system resources

## Ethics and Impact

### Why Contribute?
- Directly helps medical research
- Aids in understanding diseases
- Assists drug development
- Free way to contribute to science

### Energy Considerations
- GPU folding uses significant power
- Consider energy costs vs. contribution value
- Renewable energy makes this more sustainable
- Can configure to fold during off-peak hours

## Alternative Projects

Other distributed computing options:
- BOINC (various projects)
- World Community Grid
- Rosetta@home
- SETI@home (retired)

Folding@home specifically focuses on biomedical research.

## Notes

- Restart policy: `unless-stopped`
- Automatically restarts work on container restart
- GPU must support CUDA
- Can be paused when system resources are needed
- Contributes to legitimate scientific research
- Optional web interface for monitoring and control
- Significant power consumption when active

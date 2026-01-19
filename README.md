# Docker Logging with Vector, Loki and Grafana

This is an example setup on how to collect Docker logs in Loki using Vector. They can then be explored using Grafana.

The goal of this is to provide a basic setup that allows to have all logs in a homelab in one place. 
It can be easily extended by creating other Vector sources for different services on your homelab and process or enrich the logs before writing them to Loki.

## Quick Start

1. Clone the repository
2. Copy the environment file and adjust settings:
   ```bash
   cp .env.example .env
   ```
3. Deploy with Docker Compose:
   ```bash
   docker compose up -d
   ```

## Versions

| Tool    | Version |
|---------|---------|
| Grafana | 12.3.1  |
| Loki    | 3.6.3   |
| Vector  | 0.52.0  |

## Components

### Docker

The Docker [syslog logging driver](https://docs.docker.com/config/containers/logging/syslog/) is being utilized to write logs to a udp syslog endpoint. A tag is given that allows filtering for the different containers later.

### Vector

[Vector](https://vector.dev) is used to collect the logs and write them to Loki in this example. In more complex cases, it can also transform or enrichs them. It also writes its own logs into Loki.

### Loki

[Loki](https://grafana.com/oss/loki/) is used as a storage and query engine for the logs. In this configuration it uses the local filesystem to store the data without replication. Statistics reporting back to Grafana Labs is disabled.

**Features enabled:**
- Schema v13 (latest)
- Query result caching
- 31 days log retention
- Compactor for log cleanup

### Grafana

[Grafana](https://grafana.com/grafana/) can be used to explore the logs and to create dashboards. Loki as a datasource is provisioned at startup and logs should directly appear in the Explore view. Initial credentials are `admin:admin` (configurable via environment variables). The frontend can be reached under `localhost:3000`.

## Environment Variables

| Variable           | Default        | Description                     |
|--------------------|----------------|---------------------------------|
| GF_ADMIN_USER      | admin          | Grafana admin username          |
| GF_ADMIN_PASSWORD  | admin          | Grafana admin password          |
| GF_ROOT_URL        | http://localhost:3000 | Grafana root URL         |

## Data Persistence

Data is stored in Docker named volumes:
- `grafana_data`: Grafana dashboards and settings
- `loki_data`: Log data and indexes

## Health Checks

All services include health checks for container orchestration compatibility:
- **Grafana**: HTTP health endpoint
- **Loki**: Ready endpoint
- **Vector**: Internal tap check

## What to do next

This is a very basic setup that can and should be extended when used in a homelab context. Especially the config of Loki should be tailored to the specific needs of the respective environment. Limits and compaction should be configured according to those needs. 
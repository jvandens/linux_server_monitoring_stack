# Linux Server Monitoring Stack

This repo just defines a stack to pull into Portainer to monitor a Linux host.  
It's basic parts are: 
node-exporter ,  Prometheus ,   Grafana (optionally: InfluxDB)

# High-Level Overview

Data Collection (Exporters) → Storage (Prometheus) → Visualization (Grafana)

## Exporters (node-exporter, process-exporter, nvidia-exporter)

- These are lightweight agents that collect metrics from your system
- **node-exporter**: CPU, memory, disk, network stats from the OS
- **process-exporter**: Per-process metrics (which processes are running, resource usage)
- **nvidia-exporter**: GPU metrics (utilization, temperature, memory)
- They expose metrics on HTTP endpoints (e.g., http://node-exporter:9100/metrics)

## Prometheus (time-series database + scraper)

- Periodically "scrapes" (pulls) metrics from all exporters every 15 seconds
- Stores the time-series data in its internal database
- Provides a query language (PromQL) to retrieve and aggregate data
- Acts as the data source for Grafana


## Grafana (visualization/dashboarding)

- Connects to Prometheus as a data source
- Queries Prometheus using PromQL to get metrics
- Renders beautiful dashboards and graphs
- Doesn't store data itself - just visualizes what's in Prometheus

## InfluxDB (currently unused, but available)

- Another time-series database deployed
- Can be used for longer-term storage (Prometheus typically keeps data for 15 days by default)
- You could configure Prometheus to write data here for retention, or use Grafana to write directly to it

# Setup Notes
## Pre-deployment
Setup environment vars outlined in docker-compose.yml in Portainer

## Services

- Grafana: http://your-host:3000 (login: admin / your GRAFANA_PASSWORD)
- Prometheus: http://your-host:9090
- InfluxDB: http://your-host:8086

## Post-deployment
Connect Grafana to data source(s):

- Log into Grafana
- Go to Connections → Data Sources → Add data source
- Add Prometheus as a data source with URL: http://prometheus:9090
- Add InfluxDB as a data source with:

- Query Lang: Flux
- URL: http://influxdb:8086
- Organization: hpc
- Token: (your INFLUXDB_ADMIN_TOKEN value)
- Default Bucket: metrics

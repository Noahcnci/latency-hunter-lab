# Architecture

## System Overview

```
Traffic Generators (Alpine)
    ↓ (1Gbps links)
Arista cEOS Switch (gNMI :6030)
    ↓ (gRPC Stream)
Telegraf (gNMI Client + Starlark)
    ↓ (Line Protocol)
InfluxDB v2 (Time-Series DB)
    ↓ (Flux Queries)
Grafana (Dashboards)
```

## Network Topology

| Node | IP | Role |
|------|-----|------|
| spine-01 | 172.20.20.10 | cEOS Switch |
| traffic-gen-1 | 10.0.1.11 | Traffic Source |
| traffic-gen-2 | 10.0.1.12 | Traffic Sink |

## Data Flow

1. **Collection**: Telegraf subscribes to gNMI paths on cEOS
2. **Processing**: Starlark script calculates rates and utilization
3. **Storage**: Metrics written to InfluxDB bucket
4. **Visualization**: Grafana queries InfluxDB via Flux

## Ports

| Service | Port | Protocol |
|---------|------|----------|
| gNMI | 6030 | gRPC |
| InfluxDB | 8086 | HTTP |
| Grafana | 3000 | HTTP |
| eAPI | 80 | HTTP |

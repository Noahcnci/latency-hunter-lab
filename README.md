# Latency Hunter - Network Microburst Detection Lab

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=flat&logo=docker&logoColor=white)
![Containerlab](https://img.shields.io/badge/containerlab-ready-success)
![Status](https://img.shields.io/badge/status-production--ready-green)

Network monitoring lab for detecting sub-second traffic microbursts using streaming telemetry. Built with Containerlab, Telegraf, InfluxDB, and Grafana.

## Features

- **gNMI Streaming Telemetry**: Sub-100ms update frequency via gRPC
- **Real-Time Visualization**: Grafana dashboards with 1-second granularity
- **Traffic Generation**: Configurable burst patterns (50ms-500ms, up to 10Gbps)
- **Automated Deployment**: Infrastructure as Code with Docker Compose
- **Dual Protocol Support**: gNMI streaming (recommended) and eAPI polling

---

## Quick Start

### Prerequisites

- Ubuntu 22.04 LTS VM (4 CPU, 8 GB RAM minimum)
- Arista cEOS image
- Docker and Containerlab installed

### Installation

```bash
git clone https://github.com/Noahcnci/latency-hunter-lab.git
cd latency-hunter-lab

chmod +x scripts/install.sh
sudo ./scripts/install.sh

sudo containerlab deploy -t topology/latency-hunter.clab.yml
```

### Start Monitoring Stack

**gNMI Version (Recommended):**
```bash
docker-compose -f docker-compose-gnmi.yml up -d
```

**eAPI Version (Legacy):**
```bash
docker-compose -f docker-compose.yml up -d
```

### Access Grafana

```
http://<VM-IP>:3000
Username: admin
Password: admin
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Ubuntu 22.04 VM                         │
│                                                             │
│  ┌──────────────┐      ┌──────────────┐                     │
│  │   Traffic    │      │   Traffic    │                     │
│  │  Generator 1 │──────│  Generator 2 │                     │
│  └──────┬───────┘      └──────┬───────┘                     │
│         │                      │                            │
│         │    ┌─────────────────┴──────┐                     │
│         └────┤  Arista cEOS Switch    │                     │
│              │   (gNMI Server :6030)  │                     │
│              └──────────┬──────────────┘                    │
│                         │                                   │
│              ┌──────────▼──────────────┐                    │
│              │      Telegraf           │                    │
│              │  (gNMI/eAPI Collector)  │                    │
│              └──────────┬──────────────┘                    │
│                         │                                   │
│              ┌──────────▼──────────────┐                    │
│              │     InfluxDB v2         │                    │
│              │  (Time Series Storage)  │                    │
│              └──────────┬──────────────┘                    │
│                         │                                   │
│              ┌──────────▼──────────────┐                    │
│              │      Grafana            │                    │
│              │  (Visualization)        │                    │
│              └─────────────────────────┘                    │
└──────────────────────────────────────────────────────────────┘
```

---

## Usage

### Generate Traffic Bursts

```bash
python3 scripts/generate_microburst.py --duration 200 --rate 8G

python3 scripts/generate_microburst.py --duration 200 --rate 8G --pattern 5 --interval 10
```

### Monitor Metrics

```bash
./scripts/monitor_baseline.sh

docker logs latency-hunter-telegraf --tail 50
```

---

## Project Structure

```
latency-hunter-lab/
├── README.md
├── GNMI_SETUP.md
├── INSTALLATION.md
├── scripts/
│   ├── install.sh
│   ├── generate_microburst.py
│   └── cleanup.sh
├── topology/
│   └── latency-hunter.clab.yml
├── configs/
│   ├── switch/
│   ├── telegraf/
│   └── grafana/
├── docker-compose.yml
└── docker-compose-gnmi.yml
```

---

## Technical Specifications

### Network Configuration
- Subnet: 10.0.1.0/24
- Traffic Generator 1: 10.0.1.11
- Traffic Generator 2: 10.0.1.12
- Switch Management: 172.20.20.10

### Protocol Comparison

| Metric | eAPI (v1) | gNMI (v2) |
|--------|-----------|-----------|
| Update frequency | 1s (polling) | ~100ms (streaming) |
| Protocol | HTTP/JSON | gRPC/Protobuf |
| CPU overhead | Higher | Lower |
| Latency | ~500ms | ~50ms |

---

## Troubleshooting

See [docs/TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md) for common issues.

---

## References

- [gNMI Specification](https://github.com/openconfig/gnmi)
- [Containerlab Documentation](https://containerlab.dev)
- [Arista cEOS Guide](https://containerlab.dev/manual/kinds/ceos/)

---

## License

MIT © 2025

# Troubleshooting Guide

## Quick Fixes

### Docker Permission Denied
```bash
sudo usermod -aG docker $USER
newgrp docker
```

### Containerlab Deployment Fails
```bash
sudo containerlab destroy -a
docker network prune -f
sudo containerlab deploy -t topology/latency-hunter.clab.yml
```

### gNMI Connection Timeout
```bash
# Verify gNMI server is running
docker exec clab-latency-hunter-spine-01 Cli -c "show management api gnmi"

# Check port 6030
docker exec clab-latency-hunter-spine-01 netstat -tuln | grep 6030
```

### iperf3 Connection Refused
```bash
docker exec clab-latency-hunter-traffic-gen-2 pkill -9 iperf3
docker exec -d clab-latency-hunter-traffic-gen-2 iperf3 -s
```

### Grafana "No Data"
1. Verify traffic is running
2. Check Telegraf logs: `docker logs latency-hunter-telegraf`
3. Verify InfluxDB: `docker exec latency-hunter-influxdb influx ping`

### Grafana Permission Denied
```bash
sudo mkdir -p data/grafana data/influxdb
sudo chmod -R 777 data/
docker-compose -f docker-compose-gnmi.yml restart grafana
```

---

## Diagnostic Commands

```bash
# Docker status
docker ps -a
docker-compose -f docker-compose-gnmi.yml logs

# Network connectivity
docker exec clab-latency-hunter-traffic-gen-1 ping -c 3 10.0.1.12

# Containerlab status
sudo containerlab inspect -a
```

---

## Reset Everything

```bash
sudo containerlab destroy -a
docker-compose -f docker-compose-gnmi.yml down
docker system prune -af
docker network prune -f
```

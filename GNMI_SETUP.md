# gNMI Setup

```bash
cd /mnt/hgfs/Partage_VM/latency-hunter-lab-main

# Stop old stack
docker-compose down

# Start gNMI version
docker-compose -f docker-compose-gnmi.yml up -d

# Check logs
docker logs latency-hunter-telegraf --tail 50
```

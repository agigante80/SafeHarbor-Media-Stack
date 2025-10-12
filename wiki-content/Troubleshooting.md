# Troubleshooting

Real-world troubleshooting guide based on actual deployment scenarios and common issues encountered by users.

## 🔍 Diagnostic Commands

### Quick System Check
```bash
# Check all container status
docker compose ps

# View resource usage
docker stats --no-stream

# Check disk space
df -h

# Network connectivity test
docker exec gluetun ping 8.8.8.8
```

## 🚨 Common Issues & Solutions

### VPN Connection Problems

#### Issue: VPN Not Connecting
**Symptoms:**
```log
gluetun  | ERROR [openvpn] authentication failed
gluetun  | ERROR [firewall] cannot allow VPN connection
```

**Solutions:**
```bash
# 1. Verify credentials in .env
grep VPN_ .env

# 2. Test different server countries
VPN_SERVICE_PROVIDER=privatevpn
SERVER_COUNTRIES=Netherlands,Switzerland,Sweden

# 3. Try different protocol
OPENVPN_PROTOCOL=tcp  # instead of udp

# 4. Check provider status
docker logs gluetun --tail 50
```

#### Issue: VPN Connects But No Internet
**Symptoms:**
```log
gluetun  | INFO [openvpn] Initialization Sequence Completed
# But containers can't access internet
```

**Solutions:**
```bash
# 1. Check VPN IP assignment
docker exec gluetun wget -qO- https://ipinfo.io/ip

# 2. Test DNS resolution
docker exec gluetun nslookup google.com

# 3. Restart with fresh connection
docker compose restart gluetun
docker compose restart qbittorrent sonarr radarr
```

#### Issue: DNS Leak Testing Mode Still Active
**Problem:** Container shows testing mode even after script updates
```log
keepalive-client  | ⚠️  TESTING MODE: Simulating DNS leak - DNS location forced to US
keepalive-client  | ❌ Failed to send keepalive to http://duevite.eu:5421
```

**Root Cause:** Container running older script version despite file updates.

**Solution:**
```bash
# Restart client container to reload script
docker compose restart keepalive-client

# Verify fix - should show normal operation:
docker compose logs -f keepalive-client

# Expected after fix:
# ✅ Keepalive sent successfully at [timestamp]
# 📍 Location: Toruń, Kujawsko-Pomorskie, PL  
# 🔒 DNS: PL (WAW) - No leak detected
```

### Network Connectivity Issues

#### Issue: Keepalive Client Cannot Reach Server
**Symptoms:**
```log
keepalive-client  | ❌ Failed to send keepalive to http://duevite.eu:5421
keepalive-client  | 📍 Location: Unknown, Unknown, Unknown
```

**Diagnostic Steps:**
```bash
# 1. Test VPN connectivity from inside VPN network
docker exec keepalive-client curl -s https://ipinfo.io/json

# 2. Test keepalive server connectivity  
docker exec keepalive-client curl -v http://duevite.eu:5421/status

# 3. Check if server is accessible from host
curl -s http://localhost:5421/status

# 4. Verify container network
docker network ls
docker network inspect vpn-media_monitoring-network
```

#### Issue: Services Not Accessible from Network
**Symptoms:** Cannot access web interfaces from browser

**Solutions:**
```bash
# 1. Check if containers are running
docker compose ps

# 2. Verify port mappings
docker compose config | grep -A5 ports

# 3. Test local access
curl -I http://localhost:9804  # Sonarr
curl -I http://localhost:9802  # qBittorrent

# 4. Check firewall
sudo ufw status  # Ubuntu
sudo firewall-cmd --list-all  # CentOS/RHEL

# 5. For Synology - check DSM firewall in Control Panel
```

### Container Issues

#### Issue: Containers Failing to Start
**Symptoms:**
```log
Container xyz exited with code 1
```

**Solutions:**
```bash
# 1. Check detailed error logs
docker compose logs container-name

# 2. Check disk space
df -h

# 3. Check memory usage  
free -h

# 4. Verify permissions
ls -la gluetun/ qbittorrent/config/

# 5. Reset container state
docker compose down
docker compose up -d container-name
```

#### Issue: qBittorrent Won't Start
**Common causes and fixes:**

**Permission Issues:**
```bash
# Fix ownership
sudo chown -R $PUID_MEDIA:$PGID_MEDIA qbittorrent/config/
sudo chmod -R 755 qbittorrent/config/
```

**Port Conflicts:**
```bash
# Check if port 9802 is in use
sudo netstat -tlnp | grep 9802
sudo lsof -i :9802

# Change external port in compose.yaml if needed
```

**Config Corruption:**
```bash
# Backup and reset config
mv qbittorrent/config/ qbittorrent/config.backup/
mkdir qbittorrent/config/
docker compose restart qbittorrent
```

### Telegram Bot Issues

#### Issue: Bot Not Responding to Commands
**Diagnostic Steps:**
```bash
# 1. Check server logs for Telegram activity
docker compose logs keepalive-server | grep -i telegram

# Expected patterns:
# ✅ Telegram message sent successfully
# 📥 Telegram command received: /status  
# ✅ Status response sent
```

**Solutions:**
```bash
# 1. Verify bot token
curl "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/getMe"

# 2. Test message sending
curl -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" \
     -d "chat_id=$TELEGRAM_CHAT_ID&text=Test message"

# 3. Restart bot polling
docker compose restart keepalive-server

# 4. Check network connectivity
docker exec keepalive-server curl -s https://api.telegram.org
```

#### Issue: Bot Commands Work But No Automatic Alerts
**Check monitoring system:**
```bash
# 1. Verify keepalive client is running
docker compose logs keepalive-client --tail 20

# 2. Check if client is successfully sending data
# Should see: ✅ Keepalive sent successfully

# 3. Verify server receives data
docker compose logs keepalive-server | grep "Keepalive received"

# 4. Check alert thresholds
grep ALERT_THRESHOLD .env
```

### Performance Issues

#### Issue: High CPU Usage
**Identify the cause:**
```bash
# Check container resource usage
docker stats --no-stream

# Check system load
top
htop  # if installed
```

**Solutions:**
```bash
# 1. Limit download speeds in qBittorrent
# Go to qBittorrent WebUI → Preferences → Speed

# 2. Adjust check intervals
CHECK_INTERVAL_MINUTES=10  # Increase from 5 to 10

# 3. Reduce concurrent downloads
# Set max active torrents in qBittorrent

# 4. Monitor specific containers
docker stats qbittorrent sonarr radarr
```

#### Issue: High Memory Usage
**Check memory consumption:**
```bash
# System memory
free -h

# Container memory
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

**Solutions:**
```bash
# 1. Add memory limits to compose.yaml
services:
  qbittorrent:
    mem_limit: 1g
  sonarr:  
    mem_limit: 512m

# 2. Restart heavy containers periodically
# Add to crontab for weekly restart:
# 0 3 * * 0 cd /path/to/project && docker compose restart qbittorrent
```

### Synology-Specific Issues

#### Issue: Docker Package Problems
**Solutions:**
```bash
# 1. Ensure Docker package is latest version
# Package Center → Docker → Update

# 2. Check Docker service status
sudo systemctl status docker

# 3. Restart Docker service if needed  
sudo systemctl restart docker

# 4. Verify Docker Compose version
docker compose version
```

#### Issue: Volume Permission Problems
**Synology permissions fix:**
```bash
# 1. Get correct user/group IDs
id admin  # Usually 1026:100

# 2. Update .env file
PUID_MEDIA=1026
PGID_MEDIA=100

# 3. Fix existing permissions
sudo chown -R 1026:100 /volume1/docker/project/
sudo chmod -R 755 /volume1/docker/project/
```

#### Issue: Port Conflicts with DSM
**Common conflicting ports and solutions:**

**Port 5000 (DSM):**
```yaml
# Change keepalive server port in compose.yaml
keepalive-server:
  ports:
    - "5421:5000"  # Use 5421 instead of 5000
```

**Port 5001 (DSM HTTPS):**
```yaml
# Avoid using 5001 for any services
```

## 🔄 Container Restart Sequences

### When VPN Connection Fails
```bash
# Proper restart order
docker compose restart gluetun          # VPN gateway first
sleep 10                               # Wait for VPN connection
docker compose restart keepalive-client # Then monitoring
docker compose restart keepalive-server # Server can restart anytime

# Verify everything is working
docker compose ps
docker compose logs --tail=10 keepalive-client
```

### When Media Services Fail
```bash
# Restart media stack (order doesn't matter)
docker compose restart qbittorrent prowlarr sonarr radarr readarr bazarr

# Or restart individually as needed
docker compose restart sonarr
```

### Complete System Restart
```bash
# Nuclear option - restart everything
docker compose down
docker compose up -d

# Or with fresh images
docker compose down
docker compose pull
docker compose up -d
```

## 📈 Success Indicators

### Healthy System Logs

**Gluetun VPN:**
```log
gluetun  | INFO [openvpn] Initialization Sequence Completed
gluetun  | INFO [healthcheck] healthy!
gluetun  | INFO [ip getter] Public IP address is 185.170.104.53 (Poland...)
```

**Keepalive Client:**
```log
keepalive-client  | ✅ Keepalive sent successfully at [timestamp]
keepalive-client  | 📍 Location: Toruń, Kujawsko-Pomorskie, PL
keepalive-client  | 🔒 DNS: PL (WAW) - No leak detected
```

**Keepalive Server:**
```log
keepalive-server  | Keepalive received from synology-vpn-media - IP: 185.170.104.53
keepalive-server  | ✅ Telegram message sent successfully
```

### Verification Commands
```bash
# 1. Verify VPN is working
docker exec gluetun wget -qO- https://ipinfo.io/ip

# 2. Test container communication  
docker exec sonarr curl -s http://localhost:9696  # Prowlarr
docker exec qbittorrent curl -s http://localhost:9117  # Jackett

# 3. Check external access
curl -s http://localhost:9804  # Sonarr web interface
```

## 🛠️ Advanced Troubleshooting

### Network Analysis
```bash
# Check Docker networks
docker network ls
docker network inspect vpn-media_vpn-network
docker network inspect vpn-media_monitoring-network

# Check container networking
docker exec gluetun ip route
docker exec gluetun ip addr show
```

### Log Analysis
```bash
# Search for errors across all containers
docker compose logs | grep -i error
docker compose logs | grep -i fail
docker compose logs | grep -i timeout

# Monitor logs in real-time
docker compose logs -f --tail=50

# Export logs for analysis
docker compose logs > safeharbor-logs-$(date +%Y%m%d).log
```

### Performance Monitoring
```bash
# Monitor resource usage over time
while true; do
  docker stats --no-stream --format "{{.Name}}: CPU {{.CPUPerc}} MEM {{.MemUsage}}"
  sleep 30
done

# Check disk I/O
iostat -x 1  # If available
```

## 📞 Getting Help

### Information to Gather Before Reporting Issues

1. **System Information:**
```bash
# Platform details
uname -a
docker --version  
docker compose version

# SafeHarbor version
git log --oneline -1  # If using git
```

2. **Container Status:**
```bash
docker compose ps
docker compose logs --tail=50 > logs.txt
```

3. **Configuration (Sanitized):**
```bash
# Remove sensitive data before sharing
cp .env .env.sanitized
sed -i 's/VPN_PASSWORD=.*/VPN_PASSWORD=REDACTED/' .env.sanitized
sed -i 's/TELEGRAM_BOT_TOKEN=.*/TELEGRAM_BOT_TOKEN=REDACTED/' .env.sanitized
```

### Where to Get Help

- **GitHub Issues**: [Report bugs and issues](https://github.com/agigante80/SafeHarbor-Media-Stack/issues)
- **GitHub Discussions**: [Community support](https://github.com/agigante80/SafeHarbor-Media-Stack/discussions)  
- **Wiki**: Check other wiki pages for specific topics
- **Gluetun Issues**: [VPN-specific problems](https://github.com/qdm12/gluetun/issues)

---

**Still having issues?** Open a [GitHub issue](https://github.com/agigante80/SafeHarbor-Media-Stack/issues) with your system info and logs.
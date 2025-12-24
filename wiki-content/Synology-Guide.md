# Synology NAS Setup Guide

Complete guide for setting up SafeHarbor Media Stack on Synology NAS systems, with platform-specific optimizations and troubleshooting.

## 🏠 Synology Compatibility

SafeHarbor Media Stack has been **extensively tested and optimized** for Synology NAS systems, including:

### ✅ Tested Models
- **DS220+, DS420+, DS720+, DS920+** (Intel Celeron)
- **DS218+, DS418+, DS718+, DS918+** (Intel Celeron)  
- **DS1520+, DS1621+** (AMD Ryzen)
- **DS923+, DS1823xs+** (AMD Ryzen V1000)

### ✅ DSM Versions
- **DSM 7.0** and later (recommended)
- **DSM 6.2.3** and later (legacy support)

### ✅ Hardware Requirements
- **RAM**: 4GB minimum, 8GB+ recommended
- **CPU**: Intel/AMD x64 (ARM not supported due to container requirements)
- **Storage**: 20GB+ free space for configurations
- **Network**: Gigabit Ethernet recommended

## 🚀 Synology-Specific Setup

### Step 1: Enable Required Services

#### SSH Access
1. **Control Panel** → **Terminal & SNMP**
2. ✅ **Enable SSH service**
3. Port: `22` (or custom port)
4. Apply settings

#### Docker Package
1. **Package Center** → Search "Docker"
2. **Install Docker** package (official Synology package)
3. **Start** Docker service
4. **Note**: Requires Intel/AMD CPU (not ARM)

#### File Station Setup
1. **File Station** → Create folders:
   - `/docker/project` (for configurations)
   - `/downloads` (for download client)
   - `/media` (for organized media files)

### Step 2: System Preparation

#### Connect via SSH
```bash
# Connect to your Synology NAS
ssh admin@synology-ip-address

# Or use custom port if changed
ssh -p 2222 admin@synology-ip-address
```

#### User and Group Configuration
```bash
# Check admin user ID and group ID
id admin

# Typical Synology output:
# uid=1026(admin) gid=100(users) groups=100(users),101(administrators)

# Note these values for .env configuration:
# PUID_MEDIA=1026  
# PGID_MEDIA=100
```

#### Docker Compose Installation
```bash
# Synology doesn't include docker-compose by default
# Install using pip (Python package manager)

# Method 1: Using Synology's Python3
sudo python3 -m pip install docker-compose

# Method 2: Download binary (if pip not available)  
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installation
docker-compose --version
```

### Step 3: Synology-Optimized Configuration

#### Create .env File
```bash
# Synology-specific .env configuration
cat > .env << 'EOF'
# =============================================================================
# SYNOLOGY NAS CONFIGURATION
# =============================================================================

# VPN Configuration
VPN_SERVICE_PROVIDER=privatevpn
VPN_USER=your_vpn_username
VPN_PASSWORD=your_vpn_password
SERVER_COUNTRIES=Switzerland,Netherlands
OPENVPN_PROTOCOL=udp

# Synology Volume Paths
VOLUME_DOCKER_PROJECT=/volume1/docker/project
VOLUME_DOWNLOADS=/volume1/downloads
VOLUME_MEDIA=/volume1/media

# Synology User Permissions (adjust based on 'id admin' output)
PUID_MEDIA=1026
PGID_MEDIA=100

# Timezone (adjust to your location)
TIMEZONE=Europe/Madrid
TZ=Europe/Madrid

# Telegram Monitoring (optional)
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
VPN_SENTINEL_API_KEY=your-secure-api-key
VPN_SENTINEL_CLIENT_ID=safeharbor-media-stack
VPN_SENTINEL_CHECK_INTERVAL=300
VPN_SENTINEL_ALERT_THRESHOLD_MINUTES=15
VPN_SENTINEL_CHECK_INTERVAL_MINUTES=5
EOF
```

#### Directory Structure Creation
```bash
# Create required directories with proper permissions
sudo mkdir -p /volume1/docker/project/{gluetun,qbittorrent/config,jackett/{config,downloads}}
sudo mkdir -p /volume1/docker/project/{prowlarr/config,sonarr/config,radarr/config}
sudo mkdir -p /volume1/docker/project/{readarr/config,bazarr/config,radarr_ES/config}

# Create download and media directories
sudo mkdir -p /volume1/downloads/{complete,incomplete,watch}
sudo mkdir -p /volume1/media/{movies,tv,books,music}

# Set proper ownership (use your actual PUID/PGID)
sudo chown -R 1026:100 /volume1/docker/project/
sudo chown -R 1026:100 /volume1/downloads/
sudo chown -R 1026:100 /volume1/media/

# Set proper permissions
sudo chmod -R 755 /volume1/docker/project/
sudo chmod -R 755 /volume1/downloads/
sudo chmod -R 755 /volume1/media/
```

### Step 4: Synology-Specific Optimizations

#### Docker Resource Limits
```yaml
# Add to compose.yaml for Synology optimization
services:
  gluetun:
    mem_limit: 256m
    cpus: 0.5
    
  qbittorrent:
    mem_limit: 1g
    cpus: 1.0
    
  sonarr:
    mem_limit: 512m
    cpus: 0.5
    
  radarr:
    mem_limit: 512m  
    cpus: 0.5
```

#### Port Conflicts Resolution

Synology uses several ports that may conflict:

```yaml
# Avoid these Synology ports in compose.yaml:
# 5000 - DSM WebUI
# 5001 - DSM HTTPS WebUI  
# 80   - HTTP redirect
# 443  - HTTPS WebUI
# 22   - SSH (if enabled)

# SafeHarbor uses these ports (should be safe):
# 9802-9811 - Media services
# 5421      - VPNSentinel API
# 8080      - VPNSentinel Dashboard
# 8081      - VPNSentinel Health
```

### Step 5: Launch and Verify

#### Start SafeHarbor Stack
```bash
# Download SafeHarbor
cd /volume1/docker/
git clone https://github.com/agigante80/SafeHarbor-Media-Stack.git
cd SafeHarbor-Media-Stack

# Copy your .env file
cp /path/to/your/.env .

# Start services
docker-compose up -d

# Check status
docker-compose ps
```

#### Verify VPN Connection
```bash
# Check if VPN is working (should NOT show your real IP)
docker exec gluetun wget -qO- https://ipinfo.io/json

# Expected output shows VPN server location, not your real location
```

#### Access Services
Open these URLs in your browser (replace with your NAS IP):

| Service | URL | Purpose |
|---------|-----|---------|
| qBittorrent | `http://nas-ip:9802` | Download client |
| Sonarr | `http://nas-ip:9804` | TV shows |
| Prowlarr | `http://nas-ip:9805` | Indexer manager |
| Radarr | `http://nas-ip:9809` | Movies |
| Jackett | `http://nas-ip:9803` | Torrent indexers |

## 🔧 Synology-Specific Configuration

### DSM Integration

#### File Station Access
- **Configurations**: `/volume1/docker/project/`
- **Downloads**: `/volume1/downloads/`  
- **Media Library**: `/volume1/media/`
- **Logs**: View via Docker package in DSM

#### DSM Docker Package
```bash
# View containers in DSM
Control Panel → Docker → Container

# View logs in DSM GUI
Docker → Container → [container-name] → Details → Log
```

#### Shared Folder Configuration
1. **Control Panel** → **Shared Folder**
2. **Create** folders for media organization:
   - `downloads` → `/volume1/downloads`
   - `media` → `/volume1/media`
3. **Set permissions** for admin and users groups

### Storage Optimization

#### SSD Cache (if available)
```bash
# For NAS with SSD slots, enable read/write cache for:
/volume1/docker/     # Container configurations
/volume1/downloads/  # Active downloads

# Configure in: Storage Manager → SSD Cache
```

#### RAID Configuration
```bash
# Recommended RAID levels for media server:
# RAID 1 (2 drives): Basic protection
# RAID 5 (3+ drives): Good protection with storage efficiency  
# RAID 6 (4+ drives): Better protection, can lose 2 drives
# SHR (Synology): Flexible, recommended for mixed drive sizes
```

#### Volume Layout Recommendation
```
Volume 1 (SSD or fastest drives):
├── /docker/           # Container configurations
├── /downloads/        # Active downloads (temporary)
└── /system/          # DSM system files

Volume 2 (Large storage drives):
├── /media/           # Final media library
├── /backup/          # Backup storage  
└── /archive/         # Long-term storage
```

## 🛡️ Synology Security Configuration

### Firewall Setup
```bash
# Control Panel → Security → Firewall

# Create SafeHarbor rule:
Rule Name: SafeHarbor Media Stack
Action: Allow
Protocol: TCP
Source IP: All or specific subnet (192.168.1.0/24)
Destination port: 9802-9811,5421

# Apply rule to LAN interface
```

### User Account Security
```bash
# Create dedicated user for SafeHarbor (optional)
Control Panel → User & Group → User → Create

Username: safeharbor
Description: SafeHarbor Media Stack Service Account
Groups: users
Shell: /sbin/nologin  # No shell access
Home Directory: /volume1/homes/safeharbor

# Set quota limits appropriate for your storage
```

### Certificate Management
```bash
# For HTTPS access (optional advanced setup)
Control Panel → Security → Certificate

# Use Let's Encrypt or upload custom certificate
# Configure reverse proxy for HTTPS access to services
```

## 📊 Performance Optimization

### Resource Monitoring
```bash
# Monitor system resources in DSM
Main Menu → Resource Monitor

# Key metrics to watch:
CPU Usage: <80% sustained
Memory Usage: <85% sustained  
Network: Monitor during downloads
Storage I/O: Watch for bottlenecks
```

### Download Optimization
```bash
# Optimize qBittorrent for Synology
# Access qBittorrent WebUI → Tools → Options

Connection:
- Max connections: 100-200 (adjust based on NAS CPU)
- Max connections per torrent: 20-50
- Global max number of connections: 200

Speed:  
- Set upload limit: 10-20% of your upload bandwidth
- Enable bandwidth scheduling for off-peak hours
- Use TCP connections if UDP is unstable

Advanced:
- Enable uTP for reduced CPU load
- Disable DHT if using private trackers only
```

### Storage Performance
```bash
# Enable write caching for better performance
# (Warning: Risk of data loss on power failure)

# Check current cache status
cat /proc/sys/vm/dirty_ratio
cat /proc/sys/vm/dirty_background_ratio

# Synology optimizations (via SSH)
echo 'vm.dirty_ratio = 20' | sudo tee -a /etc/sysctl.conf
echo 'vm.dirty_background_ratio = 5' | sudo tee -a /etc/sysctl.conf
```

## 🚨 Troubleshooting Synology Issues

### Common Synology Problems

#### Docker Compose Not Found
```bash
# Error: docker-compose: command not found
# Solution: Install docker-compose

# Method 1: Via pip
sudo python3 -m pip install docker-compose

# Method 2: Via Synology Community packages
# Add SynoCommunity package source and install

# Method 3: Manual installation
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-Linux-x86_64" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

#### Permission Denied Errors
```bash
# Error: Permission denied accessing volumes
# Check current user ID
id admin

# Update .env with correct values
PUID_MEDIA=1026  # Replace with actual admin UID
PGID_MEDIA=100   # Replace with actual users GID

# Fix existing permissions
sudo chown -R 1026:100 /volume1/docker/project/
sudo chmod -R 755 /volume1/docker/project/
```

#### Services Not Starting
```bash
# Check Docker service status
sudo systemctl status docker

# Restart Docker service if needed
sudo systemctl restart docker

# Check available memory
free -h

# If low memory, restart NAS or close other applications
```

#### Port Conflicts
```bash
# Error: Port already in use
# Check what's using the port
sudo netstat -tlnp | grep :5000

# Common Synology port conflicts:
Port 5000: DSM Web interface
Port 5001: DSM HTTPS interface  
Port 80/443: Web services

# Solution: Use alternative ports in compose.yaml
```

### Network Connectivity Issues

#### VPN Not Working on Synology
```bash
# Check if tun/tap modules are loaded
lsmod | grep tun

# If not loaded, try to load manually
sudo modprobe tun

# Check kernel support
ls -la /dev/net/tun

# If missing, VPN may not be supported on your model
```

#### Container Communication Problems
```bash
# Check Docker network configuration
docker network ls
docker network inspect vpn-media_vpn-network

# Restart Docker networking
sudo systemctl restart docker
docker-compose down && docker-compose up -d
```

### Performance Issues

#### High CPU Usage
```bash
# Check which containers are using CPU
docker stats --no-stream

# Common causes:
1. Too many simultaneous downloads in qBittorrent
2. Frequent indexer searches in *arr apps
3. Large library scans

# Solutions:
# Reduce download slots in qBittorrent
# Increase indexer refresh intervals in Prowlarr
# Schedule library scans during off-peak hours
```

#### Storage Full/Slow
```bash
# Check disk usage
df -h

# Check for large log files
du -sh /volume1/docker/project/*/logs/

# Clean up large downloads
find /volume1/downloads/ -size +10G -type f

# Optimize storage in DSM
Storage Manager → Storage Pool → Optimize
```

### Backup and Recovery

#### Configuration Backup
```bash
# Create backup script for Synology
cat > /volume1/scripts/safeharbor-backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/volume1/backup/safeharbor"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Backup configurations
tar -czf "$BACKUP_DIR/safeharbor-config-$DATE.tar.gz" \
  /volume1/docker/project/SafeHarbor-Media-Stack/.env \
  /volume1/docker/project/SafeHarbor-Media-Stack/compose.yaml \
  /volume1/docker/project/*/config/

# Keep only last 7 backups
find "$BACKUP_DIR" -name "safeharbor-config-*.tar.gz" -mtime +7 -delete

echo "Backup completed: $BACKUP_DIR/safeharbor-config-$DATE.tar.gz"
EOF

chmod +x /volume1/scripts/safeharbor-backup.sh
```

#### Scheduled Backups
```bash
# Add to Synology Task Scheduler
Control Panel → Task Scheduler → Create → Scheduled Task → User-defined script

Task: SafeHarbor Backup
User: admin
Schedule: Daily at 2:00 AM
Command: /volume1/scripts/safeharbor-backup.sh
```

#### Disaster Recovery
```bash
# To restore from backup:
cd /volume1/docker/project/SafeHarbor-Media-Stack

# Stop services
docker-compose down

# Restore configuration
tar -xzf /volume1/backup/safeharbor/safeharbor-config-YYYYMMDD_HHMMSS.tar.gz -C /

# Restart services  
docker-compose up -d
```

## 🎯 Synology Best Practices

### Security Recommendations
1. **Change default ports** for DSM (5000, 5001)
2. **Enable 2-factor authentication** for admin account
3. **Regular security updates** via DSM updates
4. **Use VPN** for remote access to DSM
5. **Disable unused services** in DSM

### Performance Recommendations  
1. **Use SSD cache** for Docker volumes if available
2. **Schedule intensive tasks** during off-peak hours
3. **Monitor resource usage** regularly
4. **Optimize network settings** for your internet speed
5. **Regular maintenance** and cleanup

### Maintenance Schedule
```bash
# Weekly tasks:
- Check Docker container health
- Review download completion and cleanup
- Monitor storage usage
- Check VPN connection logs

# Monthly tasks:
- Update container images
- Clean up old downloads
- Backup configurations
- Review and organize media library

# Quarterly tasks:
- Update DSM and packages  
- Review security settings
- Optimize storage pools
- Update SafeHarbor to latest version
```

---

**Related Documentation:**
- **[[Installation Guide]]** - General setup instructions
- **[[Configuration]]** - Advanced configuration options
- **[[Troubleshooting]]** - Common issues and solutions
- **[[VPN Monitoring]]** - Monitoring system setup

**Synology-Specific Help:**
- [Synology Docker Package Guide](https://www.synology.com/en-global/support/developer_guide)
- [DSM User Guide](https://www.synology.com/en-global/support/documentation)

---

**Having Synology-specific issues?** Check the **[[Troubleshooting]]** guide or open an [issue on GitHub](https://github.com/agigante80/SafeHarbor-Media-Stack/issues) with "Synology" in the title.
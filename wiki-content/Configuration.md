# Configuration

Advanced configuration guide for SafeHarbor Media Stack, covering environment variables, customization options, and platform-specific settings.

## 🔧 Environment Configuration

### Complete .env File Reference

```bash
# =============================================================================
# VPN CONFIGURATION (REQUIRED)
# =============================================================================

# VPN Service Provider (see supported providers list)
VPN_SERVICE_PROVIDER=privatevpn

# Your VPN account credentials  
VPN_USER=your_vpn_username
VPN_PASSWORD=your_vpn_password

# Preferred server countries (comma-separated)
SERVER_COUNTRIES=Switzerland,Netherlands,Sweden

# VPN Protocol (udp recommended, try tcp if issues)
OPENVPN_PROTOCOL=udp

# Custom VPN configuration (optional)
# OPENVPN_CUSTOM_CONFIG=/path/to/config.ovpn

# =============================================================================
# PATHS CONFIGURATION (ADJUST TO YOUR SYSTEM)
# =============================================================================

# Base directory for Docker volumes and configurations
VOLUME_DOCKER_PROJECT=/volume1/docker/project    # Synology NAS
# VOLUME_DOCKER_PROJECT=/home/user/docker         # Linux
# VOLUME_DOCKER_PROJECT=/mnt/c/docker             # Windows WSL2
# VOLUME_DOCKER_PROJECT=/Users/user/docker        # macOS

# Downloads directory (completed and in-progress files)
VOLUME_DOWNLOADS=/volume1/downloads               # Synology NAS
# VOLUME_DOWNLOADS=/home/user/downloads            # Linux  
# VOLUME_DOWNLOADS=/mnt/c/downloads                # Windows WSL2
# VOLUME_DOWNLOADS=/Users/user/downloads           # macOS

# Media library directory (final organized media files)
VOLUME_MEDIA=/volume1/media                       # Synology NAS
# VOLUME_MEDIA=/home/user/media                    # Linux
# VOLUME_MEDIA=/mnt/c/media                        # Windows WSL2  
# VOLUME_MEDIA=/Users/user/media                   # macOS

# =============================================================================
# USER PERMISSIONS (IMPORTANT FOR FILE ACCESS)
# =============================================================================

# User ID and Group ID (get with: id username)
PUID_MEDIA=1000     # Linux: usually 1000, Synology: usually 1026
PGID_MEDIA=1000     # Linux: usually 1000, Synology: usually 100

# =============================================================================
# TELEGRAM MONITORING (OPTIONAL BUT RECOMMENDED)
# =============================================================================

# Telegram Bot Token (from @BotFather)
TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ

# Your Telegram Chat ID (from @userinfobot)
TELEGRAM_CHAT_ID=123456789

# API Key for VPNSentinel authentication (generate random string)
VPN_SENTINEL_API_KEY=your-secure-random-api-key-here-make-it-long
VPN_SENTINEL_CLIENT_ID=safeharbor-media-stack
VPN_SENTINEL_CHECK_INTERVAL=300
VPN_SENTINEL_ALERT_THRESHOLD_MINUTES=15
VPN_SENTINEL_CHECK_INTERVAL_MINUTES=5

# =============================================================================
# TIMEZONE CONFIGURATION
# =============================================================================

# System timezone (affects log timestamps and notifications)
TIMEZONE=Europe/Madrid
TZ=Europe/Madrid

# Available timezones:
# Europe/London, Europe/Paris, Europe/Berlin, Europe/Madrid
# America/New_York, America/Chicago, America/Denver, America/Los_Angeles  
# Asia/Tokyo, Asia/Shanghai, Asia/Kolkata
# Australia/Sydney, Australia/Melbourne
# UTC (for UTC time)

# =============================================================================
# MONITORING CONFIGURATION (OPTIONAL ADVANCED SETTINGS)
# =============================================================================

# Alert threshold (minutes before alerting about offline clients)
ALERT_THRESHOLD_MINUTES=5

# Check interval (how often to check VPN status)
CHECK_INTERVAL_MINUTES=5

# Rate limiting (requests per minute per IP)
MAX_REQUESTS_PER_MINUTE=30

# IP whitelist for API access (comma-separated, leave empty to disable)
# ALLOWED_IPS=10.51.0.1,192.168.1.100,192.168.1.0/24

# =============================================================================  
# ADVANCED VPN SETTINGS (USUALLY NOT NEEDED)
# =============================================================================

# Firewall settings (leave default unless you know what you're doing)
# FIREWALL=on
# FIREWALL_VPN_INPUT_PORTS=

# DNS settings (leave default for automatic VPN DNS)
# DOT=off
# DNS_SERVERS=

# Additional OpenVPN flags
# OPENVPN_FLAGS=--pull-filter ignore "dhcp-option DNS"
```

## 🎯 Platform-Specific Configuration

### Linux (Ubuntu/Debian/CentOS)

```bash
# Standard Linux paths
VOLUME_DOCKER_PROJECT=/home/$USER/docker/safeharbor
VOLUME_DOWNLOADS=/home/$USER/downloads  
VOLUME_MEDIA=/home/$USER/media

# Standard user permissions
PUID_MEDIA=1000
PGID_MEDIA=1000

# Get your actual user/group ID
id $USER
```

### Synology NAS

```bash
# Synology volume paths
VOLUME_DOCKER_PROJECT=/volume1/docker/project
VOLUME_DOWNLOADS=/volume1/downloads
VOLUME_MEDIA=/volume1/media

# Synology user permissions (check with: id admin)
PUID_MEDIA=1026  # Usually 1026 for admin user
PGID_MEDIA=100   # Usually 100 for users group

# Alternative Synology paths
# VOLUME_DOCKER_PROJECT=/volume2/docker/project  # If using volume2
# VOLUME_MEDIA=/volume1/Video                    # Use existing Video folder
```

### Windows with WSL2

```bash
# Windows paths accessible from WSL2
VOLUME_DOCKER_PROJECT=/mnt/c/Users/$USER/docker/safeharbor
VOLUME_DOWNLOADS=/mnt/c/Users/$USER/Downloads/safeharbor
VOLUME_MEDIA=/mnt/c/Users/$USER/Videos/media

# WSL2 user permissions
PUID_MEDIA=1000
PGID_MEDIA=1000
```

### macOS with Docker Desktop

```bash
# macOS paths
VOLUME_DOCKER_PROJECT=/Users/$USER/docker/safeharbor  
VOLUME_DOWNLOADS=/Users/$USER/Downloads/safeharbor
VOLUME_MEDIA=/Users/$USER/Movies/media

# macOS user permissions  
PUID_MEDIA=501   # Usually 501 for first user
PGID_MEDIA=20    # Usually 20 for staff group

# Get your actual user/group ID
id $USER
```

## 🔐 Security Configuration

### API Security Settings

```bash
# Generate secure API key
VPN_SENTINEL_API_KEY=$(openssl rand -hex 32)

# IP Whitelisting (recommended for production)
ALLOWED_IPS=10.51.0.1,192.168.1.0/24,172.16.0.0/12

# Rate limiting (adjust based on your needs)
MAX_REQUESTS_PER_MINUTE=60  # Higher for multiple clients
```

### VPN Security Settings

```bash
# Use strongest encryption (if supported by provider)
OPENVPN_PROTOCOL=udp
# OPENVPN_FLAGS=--cipher AES-256-GCM --auth SHA256

# Kill switch (blocks traffic if VPN fails)
FIREWALL=on

# DNS leak prevention (automatic with Gluetun)
# DOT=on  # DNS over TLS for extra security
```

## 📊 Performance Configuration

### Resource Optimization

```bash
# For systems with limited RAM (4GB or less)
# Add to compose.yaml under each service:
# mem_limit: 512m      # For *arr services
# mem_limit: 1g        # For qBittorrent
# mem_limit: 256m      # For monitoring services
```

### Download Optimization

Configure these settings in qBittorrent WebUI:

```yaml
# Connection Settings:
Maximum connections: 200
Maximum connections per torrent: 20
Maximum uploads per torrent: 10

# Speed Settings (adjust for your connection):
Global download limit: 0      # Unlimited (or set limit)
Global upload limit: 1000     # 1MB/s (adjust as needed)

# Bandwidth Scheduling:
Enable: True
From: 02:00 to 08:00         # Night hours for full speed
Alternative speeds during: Working hours
```

### Monitoring Performance

```bash
# Reduce monitoring frequency for lower-powered systems
CHECK_INTERVAL_MINUTES=10     # Every 10 minutes instead of 5
ALERT_THRESHOLD_MINUTES=15    # Allow more time before alerts

# Increase for high-availability requirements
CHECK_INTERVAL_MINUTES=2      # Every 2 minutes
ALERT_THRESHOLD_MINUTES=3     # Quick alerts
```

## 🎛️ Service-Specific Configuration

### qBittorrent Advanced Settings

Access qBittorrent WebUI → Tools → Options:

#### Connection Tab
```
Port: 8085 (internal container port)
UPnP/NAT: Disabled (using VPN)
Encryption: Require encryption
```

#### Downloads Tab
```
Default Save Path: /downloads/complete
Keep incomplete torrents in: /downloads/incomplete
Copy .torrent files to: /downloads/watch
Copy .torrent files for finished downloads to: /downloads/completed
```

#### BitTorrent Tab
```
Privacy:
☑ Enable DHT network
☑ Enable PeX
☑ Enable LSD
☑ Enable anonymous mode

Torrent Queueing:
Maximum active downloads: 5
Maximum active uploads: 5
Maximum active torrents: 10
```

### Prowlarr Configuration

Access Prowlarr WebUI → Settings:

#### Indexers Tab
```
Add popular indexers:
- 1337x, RARBG, The Pirate Bay (public)
- Private trackers (if you have accounts)

Test each indexer after adding
```

#### Apps Tab
```
Add Applications:
- Sonarr: http://localhost:8989
- Radarr: http://localhost:7878  
- Readarr: http://localhost:8787

Sync categories and tags automatically
```

### *arr Applications Configuration

Common settings for Sonarr, Radarr, Readarr:

#### Media Management
```
Root Folders:
- TV Shows: /media/tv
- Movies: /media/movies  
- Books: /media/books

File Naming:
☑ Rename episodes/movies
☑ Replace illegal characters
☑ Use season folders (for TV)
```

#### Download Clients
```
qBittorrent:
- Host: localhost
- Port: 8085
- Username: admin
- Password: [your qbittorrent password]
- Category: [tv/movies/books]
```

## 🌐 Network Configuration

### Port Customization

To change external ports, edit `compose.yaml`:

```yaml
# Example: Change qBittorrent from 9802 to 8080
qbittorrent:
  ports:
    - "8080:8085"  # host:container

# Example: Change Sonarr from 9804 to 8989  
sonarr:
  ports:
    - "8989:8989"  # Use same port as internal
```

### Firewall Configuration

#### UFW (Ubuntu Firewall)
```bash
# Allow SafeHarbor ports
sudo ufw allow 9802:9811/tcp    # Media services
sudo ufw allow 5421/tcp         # VPNSentinel API
sudo ufw allow 8080/tcp         # VPNSentinel Dashboard
sudo ufw allow 8081/tcp         # VPNSentinel Health

# Or allow from specific subnet only
sudo ufw allow from 192.168.1.0/24 to any port 9802:9811
```

#### Synology Firewall
```
Control Panel → Security → Firewall → Edit Rules
Add rules for ports 9802-9811 (TCP)
Source: All or specific IP range
```

## 🔄 Environment Updates

### Applying Configuration Changes

```bash
# After editing .env file, restart affected services:
docker compose down
docker compose up -d

# Or restart specific service:
docker compose restart gluetun  # Restart VPN with new settings
docker compose restart vpn-sentinel-server  # Update Telegram settings
```

### Backup Configuration

```bash
# Backup your configuration
cp .env .env.backup.$(date +%Y%m%d)

# Backup all configs
tar -czf safeharbor-config-backup-$(date +%Y%m%d).tar.gz \
  .env compose.yaml */config/
```

## 🐛 Configuration Troubleshooting

### Common Configuration Issues

#### Path Problems
```bash
# Check if paths exist and are accessible
ls -la $VOLUME_DOCKER_PROJECT
ls -la $VOLUME_DOWNLOADS  
ls -la $VOLUME_MEDIA

# Fix permissions if needed
sudo chown -R $PUID_MEDIA:$PGID_MEDIA $VOLUME_DOCKER_PROJECT
```

#### User Permission Issues
```bash
# Find your actual user/group ID
id $(whoami)

# Update .env with correct values
PUID_MEDIA=1000  # Replace with actual UID
PGID_MEDIA=1000  # Replace with actual GID
```

#### VPN Connection Issues
```bash
# Test VPN credentials manually
echo "VPN_USER: $VPN_USER"
echo "VPN_SERVICE_PROVIDER: $VPN_SERVICE_PROVIDER"

# Try different server countries
SERVER_COUNTRIES=Germany,France,Spain

# Try TCP instead of UDP
OPENVPN_PROTOCOL=tcp
```

### Validation Commands

```bash
# Validate .env file syntax
cat .env | grep -E '^[A-Z_]+=.*$' | wc -l  # Count valid env vars

# Test Telegram configuration
curl -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" \
     -d "chat_id=$TELEGRAM_CHAT_ID&text=Config test from SafeHarbor"

# Verify Docker Compose syntax
docker compose config
```

## 📚 Related Documentation

- **[[Installation Guide]]** - Basic setup and initial configuration
- **[[Synology Guide]]** - NAS-specific configuration details  
- **[[Troubleshooting]]** - Common configuration problems and solutions
- **[[VPN Monitoring]]** - Monitoring system configuration
- **[[Telegram Bot]]** - Bot setup and notification configuration

---

**Need help with specific configuration?** Check the **[[Troubleshooting]]** guide or open an [issue on GitHub](https://github.com/agigante80/SafeHarbor-Media-Stack/issues).
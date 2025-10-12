# Installation Guide

Complete step-by-step guide to setting up SafeHarbor Media Stack from scratch.

## 📋 Prerequisites

### System Requirements
- **Operating System**: Linux, Windows (WSL2), macOS, or Synology DSM
- **Docker**: Version 20.10+ with Docker Compose v2
- **Memory**: Minimum 4GB RAM (8GB+ recommended)
- **Storage**: 50GB+ free space for configurations and downloads
- **Network**: Stable internet connection for VPN and downloads

### Required Accounts
- **VPN Provider**: Account with supported provider (PrivateVPN recommended)
- **Telegram** (Optional): For monitoring notifications
- **Domain/DDNS** (Optional): For remote access

### Supported VPN Providers
This stack uses [Gluetun](https://github.com/qdm12/gluetun) with support for 25+ providers:

**Popular Providers:**
- PrivateVPN, NordVPN, ExpressVPN, Surfshark
- Mullvad, ProtonVPN, PureVPN, CyberGhost
- Private Internet Access, IPVanish, Windscribe

**Protocol Support:**
- OpenVPN (all providers)
- Wireguard (most providers)

See [Gluetun documentation](https://github.com/qdm12/gluetun/wiki) for complete provider list.

## 🚀 Installation Steps

### Step 1: System Preparation

#### Linux/Ubuntu/Debian
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker

# Verify installation
docker --version
docker compose version
```

#### Synology NAS
1. Open **Package Center**
2. Install **Docker** package
3. Enable SSH access in **Control Panel** → **Terminal & SNMP**
4. Connect via SSH: `ssh admin@synology-ip`

#### Windows (WSL2)
```powershell
# Install WSL2 and Docker Desktop
wsl --install
# Download and install Docker Desktop for Windows
# Enable WSL2 integration in Docker Desktop settings
```

### Step 2: Download SafeHarbor

```bash
# Clone repository
git clone https://github.com/agigante80/SafeHarbor-Media-Stack.git
cd SafeHarbor-Media-Stack

# Or download and extract ZIP
wget https://github.com/agigante80/SafeHarbor-Media-Stack/archive/main.zip
unzip main.zip
cd SafeHarbor-Media-Stack-main
```

### Step 3: Environment Configuration

#### Create Environment File
```bash
# Copy example environment file
cp .env.example .env

# Edit configuration
nano .env  # or use your preferred editor
```

#### Essential Settings (.env)
```bash
# =============================================================================
# VPN CONFIGURATION (REQUIRED)
# =============================================================================
VPN_SERVICE_PROVIDER=privatevpn
VPN_USER=your_vpn_username
VPN_PASSWORD=your_vpn_password
SERVER_COUNTRIES=Switzerland,Netherlands
OPENVPN_PROTOCOL=udp

# =============================================================================
# PATHS CONFIGURATION (ADJUST TO YOUR SYSTEM)
# =============================================================================
# Base directory for Docker volumes
VOLUME_DOCKER_PROJECT=/volume1/docker/project  # Synology
# VOLUME_DOCKER_PROJECT=/home/user/docker       # Linux
# VOLUME_DOCKER_PROJECT=/mnt/c/docker           # Windows WSL2

# Downloads directory (where completed files go)
VOLUME_DOWNLOADS=/volume1/downloads             # Synology  
# VOLUME_DOWNLOADS=/home/user/downloads          # Linux
# VOLUME_DOWNLOADS=/mnt/c/downloads              # Windows WSL2

# Media library directory
VOLUME_MEDIA=/volume1/media                     # Synology
# VOLUME_MEDIA=/home/user/media                  # Linux  
# VOLUME_MEDIA=/mnt/c/media                      # Windows WSL2

# =============================================================================
# USER PERMISSIONS (GET WITH: id username)
# =============================================================================
PUID_MEDIA=1000  # Your user ID
PGID_MEDIA=1000  # Your group ID

# =============================================================================
# TELEGRAM MONITORING (OPTIONAL BUT RECOMMENDED)
# =============================================================================
TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ
TELEGRAM_CHAT_ID=123456789
KEEPALIVE_API_KEY=your-secure-api-key-here

# =============================================================================
# ADVANCED SETTINGS (USUALLY OK AS DEFAULT)
# =============================================================================
TIMEZONE=Europe/Madrid
TZ=Europe/Madrid
```

### Step 4: Directory Structure Setup

#### Automatic Setup (Recommended)
```bash
# Create all required directories
./setup-directories.sh
```

#### Manual Setup
```bash
# Create base directories
mkdir -p gluetun
mkdir -p qbittorrent/config
mkdir -p jackett/{config,downloads}
mkdir -p prowlarr/config
mkdir -p sonarr/config  
mkdir -p radarr/config
mkdir -p readarr/config
mkdir -p bazarr/config
mkdir -p radarr_ES/config

# Create media structure
mkdir -p media/{movies,tv,books,downloads}
mkdir -p downloads/{complete,incomplete}

# Set permissions (Linux/Synology)
sudo chown -R $USER:$USER gluetun/ qbittorrent/ jackett/ prowlarr/ sonarr/ radarr/ readarr/ bazarr/
```

### Step 5: Telegram Bot Setup (Optional)

#### Create Telegram Bot
1. Open Telegram and message [@BotFather](https://t.me/botfather)
2. Send `/newbot` command
3. Choose a name: `My VPN Media Bot`
4. Choose a username: `myvpnmedia_bot` (must end in 'bot')
5. Copy the **Bot Token**: `1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ`

#### Get Chat ID
**Method 1 - Using @userinfobot:**
1. Message [@userinfobot](https://t.me/userinfobot)
2. Copy your **Chat ID**: `123456789`

**Method 2 - API call:**
```bash
# Replace YOUR_BOT_TOKEN with actual token
curl "https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates"

# Send a message to your bot first, then look for chat.id in response
```

#### Test Setup
```bash
# Test bot communication (replace with your values)
curl -X POST "https://api.telegram.org/bot1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ/sendMessage" \
     -d "chat_id=123456789&text=SafeHarbor setup test message"
```

### Step 6: Start the Stack

```bash
# Start all services
docker compose up -d

# Check status
docker compose ps

# View logs
docker compose logs -f gluetun keepalive-client
```

**Expected output:**
```
✓ Container gluetun                Running
✓ Container keepalive-server       Running  
✓ Container keepalive-client       Running
✓ Container qbittorrent           Running
✓ Container prowlarr              Running
✓ Container sonarr                Running
✓ Container radarr                Running
```

### Step 7: Initial Service Configuration

#### 1. qBittorrent Setup
1. Access: `http://your-server:9802`
2. Login: `admin` / `adminadmin`
3. **CHANGE PASSWORD IMMEDIATELY**
4. Go to **Settings** → **Downloads**:
   - Default Save Path: `/downloads/complete`
   - Incomplete torrents: `/downloads/incomplete`
5. **Connection** settings:
   - Listening Port: `8085`
   - Use UPnP: Disabled

#### 2. Prowlarr Setup (Index Manager)
1. Access: `http://your-server:9805`
2. **Settings** → **Indexers** → Add indexers
3. **Settings** → **Apps** → Add Applications:
   - Add Sonarr: `http://localhost:8989`
   - Add Radarr: `http://localhost:7878`
   - Add Readarr: `http://localhost:8787`
4. **Settings** → **Download Clients**:
   - Add qBittorrent: `http://localhost:8085`

#### 3. Sonarr Setup (TV Shows)
1. Access: `http://your-server:9804`
2. **Settings** → **Media Management**:
   - Root Folder: `/media/tv`
3. **Settings** → **Download Clients**:
   - Add qBittorrent: `http://localhost:8085`
4. **Settings** → **Indexers**: Will auto-sync from Prowlarr

#### 4. Radarr Setup (Movies)  
1. Access: `http://your-server:9809`
2. **Settings** → **Media Management**:
   - Root Folder: `/media/movies`
3. **Settings** → **Download Clients**:
   - Add qBittorrent: `http://localhost:8085`

## ✅ Verification Steps

### Check VPN Connection
```bash
# Verify VPN IP (should NOT be your real IP)
docker exec gluetun wget -qO- https://ipinfo.io/json

# Check VPN logs
docker logs gluetun --tail 20
```

**Expected VPN output:**
```json
{
  "ip": "185.170.104.53",
  "city": "Toruń", 
  "region": "Kujawsko-Pomorskie",
  "country": "PL",
  "org": "AS50599 DATASPACE P.S.A."
}
```

### Test Container Communication
```bash
# Test internal networking
docker exec sonarr curl -s http://localhost:9696  # Prowlarr
docker exec qbittorrent curl -s http://localhost:9117  # Jackett
```

### Verify Telegram Bot
```bash
# Check keepalive server logs
docker logs keepalive-server --tail 10

# Send test command to bot
# In Telegram: /ping
```

**Expected bot response:**
```
🏓 Pong!

Server time: 2025-10-12 15:30:22 CEST
Active clients: 1
Alert threshold: 5 minutes
Check interval: 5 minutes
```

## 🔧 Platform-Specific Notes

### Synology NAS
```bash
# Use Synology paths
VOLUME_DOCKER_PROJECT=/volume1/docker/project
VOLUME_DOWNLOADS=/volume1/downloads  
VOLUME_MEDIA=/volume1/media

# Get correct PUID/PGID
id admin  # Usually 1026:100 on Synology
```

### Windows WSL2
```bash
# Use Windows paths accessible from WSL
VOLUME_DOCKER_PROJECT=/mnt/c/docker
VOLUME_DOWNLOADS=/mnt/c/downloads
VOLUME_MEDIA=/mnt/c/media

# Ensure Docker Desktop WSL2 integration is enabled
```

### Raspberry Pi / ARM64
```bash
# All containers support ARM64 architecture
# May need to adjust memory limits for Pi 4 with 4GB RAM
# Consider using external storage for downloads
```

## 🛠️ Post-Installation

### Security Hardening
- Change all default passwords
- Enable 2FA where supported
- Regular backup of configurations
- Monitor system resources

### Performance Optimization  
- Adjust download limits based on bandwidth
- Configure proper indexer refresh intervals
- Set up download scheduling during off-peak hours

### Backup Strategy
```bash
# Backup configurations
tar -czf safeharbor-backup-$(date +%Y%m%d).tar.gz \
  gluetun/ qbittorrent/config/ sonarr/config/ radarr/config/ \
  prowlarr/config/ readarr/config/ bazarr/config/ .env
```

## ❓ Next Steps

1. **[[Configuration]]** - Advanced settings and customization
2. **[[Telegram Bot]]** - Complete bot functionality  
3. **[[Troubleshooting]]** - If you encounter any issues
4. **[[VPN Monitoring]]** - Understanding the monitoring system

---

**Having issues?** Check the **[[Troubleshooting]]** guide or open an [issue on GitHub](https://github.com/agigante80/SafeHarbor-Media-Stack/issues).
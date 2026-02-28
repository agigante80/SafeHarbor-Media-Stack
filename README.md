# SafeHarbor Media Stack 🎬🔒
*VPN-Protected Media Automation Stack for Synology NAS*

A Docker Compose configuration for automated media management with VPN protection, specifically tested and documented for **Synology NAS systems**. Features the popular Servarr (*arr) applications, qBittorrent, and [**VPN Sentinel**](https://github.com/agigante80/VPNSentinel) monitoring with Telegram notifications.

## 🎯 What This Project Actually Is

This is a **Docker Compose configuration** that integrates existing open-source tools:
- **[Gluetun](https://github.com/qdm12/gluetun)** - VPN gateway container
- **[Servarr Apps](https://wiki.servarr.com/)** - Media automation suite (Sonarr, Radarr, etc.)
- **[qBittorrent](https://www.qbittorrent.org/)** - Download client
- **[VPN Sentinel](https://github.com/agigante80/VPNSentinel)** - VPN monitoring with Telegram alerts (by [@agigante80](https://github.com/agigante80))

**What makes this configuration useful:**
- ✅ **Synology NAS Tested**: Documented Gluetun version compatibility for Synology DSM 7.x
- ✅ **VPN Sentinel Integration**: Pre-configured monitoring with DNS leak detection
- ✅ **Working Configuration**: Battle-tested setup that actually works on Synology
- ✅ **Media Server Agnostic**: No Plex/Jellyfin - use your own media server

## 🏠 Synology NAS Compatibility

**Tested Hardware:** Synology DS220+ with DSM 7.x  
**Status:** ✅ Fully Working

### Critical Synology Issue: Gluetun Version Compatibility

**⚠️ IMPORTANT:** Gluetun v3.41.0+ has a breaking change for Synology NAS users.

| Gluetun Version | Synology Compatible | Notes |
|-----------------|---------------------|-------|
| **v3.40.4** (Dec 24, 2024) | ✅ Works out-of-box | **Recommended** - No sysctls needed |
| **v3.41.0+** (Dec 25, 2024+) | ⚠️ Requires sysctls | Needs kernel parameters |

**Symptoms of v3.41.0+ without sysctls:**
```
netfilter query: netlink receive: invalid argument
Container restart loop
```

**Solutions:**
1. **Recommended:** Use Gluetun v3.40.4 (this repo's default)
2. **Alternative:** Add sysctls for v3.41.0+:
   ```yaml
   sysctls:
     - net.ipv4.conf.all.src_valid_mark=1
     - net.ipv6.conf.all.disable_ipv6=0
   ```

This repository pins Gluetun to `v3.40.4` for maximum Synology compatibility.

## 🔗 Related Projects & Attribution

### This Stack Uses:
- **[VPN Sentinel](https://github.com/agigante80/VPNSentinel)** by [@agigante80](https://github.com/agigante80) - VPN monitoring with Telegram notifications, DNS leak detection, and interactive bot commands
- **[Gluetun](https://github.com/qdm12/gluetun)** by [@qdm12](https://github.com/qdm12) - Lightweight VPN client container
- **[Servarr](https://wiki.servarr.com/)** - Suite of media automation applications

### Similar Media Stack Projects:
- [**navilg/media-stack**](https://github.com/navilg/media-stack) - Complete media server with Plex
- [**DonMcD/ultimate-plex-stack**](https://github.com/DonMcD/ultimate-plex-stack) - Ultimate Plex server stack

### 📊 Comparison: Why Use This Configuration?

| Feature | This Stack | navilg/media-stack | ultimate-plex-stack |
|---------|------------|-------------------|---------------------|
| **Synology Tested** | ✅ DS220+, DSM 7.x | ⚠️ Generic Docker | ⚠️ Generic Docker |
| **Gluetun Version Docs** | ✅ v3.40.4 pinned | ⚠️ Latest (may break) | ⚠️ Latest (may break) |
| **VPN Monitoring** | ✅ VPN Sentinel | ❌ None | ❌ None |
| **Telegram Alerts** | ✅ Via VPN Sentinel | ❌ No | ❌ No |
| **DNS Leak Detection** | ✅ Automated | ❌ Manual | ❌ Manual |
| **Interactive Bot** | ✅ VPN status check | ❌ No | ❌ No |
| **Media Server** | ❌ Agnostic | ✅ Plex | ✅ Plex + Tautulli |
| **Request Management** | ❌ Use Trakt | ✅ Overseerr | ✅ Overseerr |
| **Dashboard** | ❌ None | ❌ None | ✅ Organizr |
| **Architecture** | Standard | Standard | Standard |

**When to use this configuration:**
- ✅ You have a Synology NAS (especially DS220+ or similar)
- ✅ You want VPN monitoring with Telegram notifications
- ✅ You prefer Trakt over Overseerr for content management
- ✅ You want acquisition-only (bring your own media server)
- ✅ You need working Gluetun version documentation

## ✨ Features

### 🛡️ VPN & Security
- **VPN Gateway**: All traffic routed through Gluetun (supports 20+ VPN providers)
- **VPN Monitoring**: [VPN Sentinel](https://github.com/agigante80/VPNSentinel) with real-time status tracking
- **DNS Leak Detection**: Automated monitoring and Telegram alerts
- **Connection Alerts**: Instant notifications when VPN disconnects or reconnects

### 📱 Telegram Integration (via VPN Sentinel)
- **Interactive Bot**: `/status`, `/ping`, `/help` commands
- **Real-time Alerts**: VPN connection loss, restoration, DNS leaks
- **Location Tracking**: Shows current VPN server location and ISP
- **Status Reports**: On-demand VPN status with IP, location, and uptime

### 📺 Media Automation
- **Sonarr**: TV show automation and management
- **Radarr**: Movie automation and management
- **Readarr**: Book and audiobook automation
- **Bazarr**: Subtitle automation
- **Prowlarr**: Unified indexer management
- **Jackett**: Additional torrent indexer support
- **FlareSolverr**: Cloudflare bypass for protected sites
- **qBittorrent**: Feature-rich download client with WebUI

### 🏠 Synology Optimized
- **Tested Hardware**: DS220+ with DSM 7.x
- **Version Pinning**: Gluetun v3.40.4 for stability
- **Path Configuration**: Pre-configured for `/volume1/` structure
- **Permission Handling**: PUID/PGID configuration included

### 🎯 Design Philosophy
- **Media Server Agnostic**: No Plex/Jellyfin/Emby - bring your own
- **Acquisition Focus**: Download and organize, not serve
- **Standard Architecture**: Uses proven `network_mode: service:gluetun` pattern
- **External Port Access**: All services accessible from local network

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                      Host Network                             │
│  ┌───────────────────────────────────────────────────────┐   │
│  │              Gluetun VPN Gateway                      │   │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐ │   │
│  │  │qBittorrent│ │  Sonarr  │ │  Radarr  │ │ Jackett │ │   │
│  │  └──────────┘ └──────────┘ └──────────┘ └─────────┘ │   │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐ │   │
│  │  │ Prowlarr │ │  Bazarr  │ │ Readarr  │ │FlareSlvr│ │   │
│  │  └──────────┘ └──────────┘ └──────────┘ └─────────┘ │   │
│  │  ┌─────────────────────────────────────────────────┐ │   │
│  │  │         VPN Sentinel Client                     │ │   │
│  │  │      (Monitors VPN from inside tunnel)          │ │   │
│  │  └─────────────────────────────────────────────────┘ │   │
│  └───────────────────────────────────────────────────────┘   │
│                          │                                    │
│                          │ Internet (via VPN)                │
│                          │                                    │
│                          │  ┌──────────────────────────┐     │
│                          │  │  VPN Sentinel Server     │     │
│                          └─►│  (External monitoring)   │     │
│                             │  + Telegram Bot          │     │
│                             └──────────────────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Network Architecture Details

**VPN Routing (Standard Pattern):**
- All services use `network_mode: "service:gluetun"`
- Traffic from *arr apps and qBittorrent routes through Gluetun
- Containers communicate via `localhost` (shared network namespace)

**VPN Sentinel Monitoring:**
- **Client**: Runs inside VPN tunnel, reports VPN IP and location
- **Server**: External server with real IP, receives client reports
- **Telegram Bot**: Integrated in server, sends alerts and responds to commands
- **DNS Leak Detection**: Compares VPN location with DNS resolver location

**Why This Architecture:**
- ✅ Simple: Standard Docker Compose `network_mode` pattern
- ✅ Secure: All acquisition traffic forced through VPN
- ✅ Monitored: VPN Sentinel detects failures and leaks
- ✅ Maintainable: Uses well-established open-source tools

## 🔌 Why Gluetun?

[Gluetun](https://github.com/qdm12/gluetun) is a lightweight VPN client container that supports **20+ VPN providers** out of the box:

**Major Providers:** AirVPN, ExpressVPN, IPVanish, IVPN, Mullvad, NordVPN, Private Internet Access, PrivateVPN, ProtonVPN, PureVPN, Surfshark, TorGuard, Windscribe, and more.

**Protocol Support:**
- **OpenVPN**: Full support for all providers
- **Wireguard**: Native and custom configuration support

**Why use Gluetun:**
- ✅ Single container routes all other containers' traffic
- ✅ No VPN client needed on host system
- ✅ Easy provider switching via environment variables
- ✅ Built-in health checks and auto-reconnect
- ✅ Kill switch prevents leaks if VPN disconnects

See the [Gluetun GitHub repository](https://github.com/qdm12/gluetun) for the complete provider list and documentation.

## 📦 Stack Components

### Core Services (9 Containers)

| Service | Purpose | Port | Notes |
|---------|---------|------|-------|
| **[Gluetun](https://github.com/qdm12/gluetun)** | VPN Gateway | - | v3.40.4 pinned for Synology |
| **[qBittorrent](https://www.qbittorrent.org/)** | Download Client | 9802 | Routes through Gluetun |
| **[Sonarr](https://sonarr.tv/)** | TV Automation | 9804 | Part of Servarr suite |
| **[Radarr](https://radarr.video/)** | Movie Automation | 9809 | Part of Servarr suite |
| **[Readarr](https://readarr.com/)** | Book Automation | 9808 | Part of Servarr suite |
| **[Bazarr](https://www.bazarr.media/)** | Subtitle Automation | 9810 | Integrates with Sonarr/Radarr |
| **[Prowlarr](https://prowlarr.com/)** | Indexer Manager | 9805 | Unified indexer management |
| **[Jackett](https://github.com/Jackett/Jackett)** | Indexer Proxy | 9803 | Additional indexer support |
| **[FlareSolverr](https://github.com/FlareSolverr/FlareSolverr)** | Cloudflare Bypass | 9806 | For protected sites |

### Optional Service

| Service | Purpose | Notes |
|---------|---------|-------|
| **[VPN Sentinel](https://github.com/agigante80/VPNSentinel)** | VPN Monitoring | Client + Server with Telegram bot |

**About Servarr:**  
[Servarr](https://wiki.servarr.com/) is a suite of applications for automated media management. The *arr apps provide consistent APIs and interfaces for different media types (TV, movies, books, music).

## 🚀 Quick Start

### 1. Prerequisites

**Required:**
- Synology NAS with Docker package installed (tested on DS220+ with DSM 7.x)
- VPN provider account (PrivateVPN, NordVPN, etc.)

**Optional but Recommended:**
- Telegram bot token (for VPN Sentinel monitoring)

### 2. Telegram Bot Setup for VPN Sentinel (Optional)

[VPN Sentinel](https://github.com/agigante80/VPNSentinel) provides Telegram notifications for VPN status. To enable:

1. **Create a Telegram Bot:**
   - Message [@BotFather](https://t.me/botfather) on Telegram
   - Send `/newbot` and follow the prompts
   - Save the **Bot Token** (format: `1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ`)

2. **Get Your Chat ID:**
   - Message [@userinfobot](https://t.me/userinfobot) to get your Chat ID
   - Or visit `https://api.telegram.org/bot<BOT_TOKEN>/getUpdates` after messaging your bot

3. **Test the Bot:**
   ```bash
   curl -X POST "https://api.telegram.org/bot<BOT_TOKEN>/sendMessage" \
        -d "chat_id=<CHAT_ID>&text=Test from SafeHarbor Stack"
   ```

### 3. Installation on Synology NAS

**SSH into your Synology:**
```bash
ssh admin@your-synology-ip -p 22
```

**Clone and configure:**
```bash
# Navigate to your Docker project directory
cd /volume1/docker/project

# Clone this repository
git clone <repository-url> VPN-media
cd VPN-media

# Copy and edit environment file
cp .env.example .env
nano .env  # Configure VPN credentials, paths, and Telegram tokens
```

**Configure Environment Variables** (`.env`):
```bash
# VPN Configuration
VPN_SERVICE_PROVIDER=privatevpn  # or nordvpn, mullvad, etc.
VPN_USER=your_vpn_username
VPN_PASSWORD=your_vpn_password
SERVER_COUNTRIES=Switzerland,Netherlands

# Synology Paths
VOLUME_DOCKER_PROJECT=/volume1/docker/project
VOLUME_DOWNLOADS=/volumeUSB2/usbshare/Downloader/downloads
VOLUME_MEDIA=/volume1/Media

# User IDs (typically 1026 for Synology admin)
PUID_MEDIA=1026
PGID_MEDIA=100

# Telegram (optional - for VPN Sentinel)
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

**Start the stack:**
```bash
# Create necessary directories (if needed)
mkdir -p {gluetun,qbittorrent/config,jackett/config,prowlarr/config,sonarr/config,radarr/config,readarr/config,bazarr/config}

# Start all containers
docker compose up -d

# Check status
docker compose ps

# View logs
docker compose logs -f gluetun
```

### 4. First-Time Service Configuration

Access services from your local network using your Synology IP:

1. **qBittorrent** - `http://synology-ip:9802`
   - Default: `admin` / `adminadmin` (**change immediately**)
   - Set download path: `/downloads`

2. **Prowlarr** - `http://synology-ip:9805`
   - Add indexers (torrent sites)
   - Connect to Sonarr/Radarr via API
   - Add FlareSolverr: `http://localhost:8191`

3. **Sonarr** - `http://synology-ip:9804`
   - Add Prowlarr indexers
   - Add qBittorrent: `http://localhost:8085`
   - Configure root folder: `/media/TV Shows`

4. **Radarr** - `http://synology-ip:9809`
   - Similar to Sonarr configuration
   - Configure root folder: `/media/Movies`

5. **Bazarr** - `http://synology-ip:9810`
   - Connect to Sonarr and Radarr
   - Add subtitle providers

6. **Optional: VPN Sentinel Bot Commands**
   - Send `/status` to your Telegram bot to check VPN
   - Send `/ping` to verify bot connectivity
   - Send `/help` for command list

### 🎭 Media Server Integration (Bring Your Own)

This stack is **intentionally media player agnostic** - no Plex/Jellyfin/Emby included. The downloaded media is organized and ready for any media server to scan.

**Popular Media Server Options:**
- **[Jellyfin](https://jellyfin.org/)** - Free and open-source
- **[Plex](https://www.plex.tv/)** - Feature-rich with premium options
- **[Emby](https://emby.media/)** - Alternative to Plex

Point your media server to the configured media directories (e.g., `/volume1/Media/TV Shows`, `/volume1/Media/Movies`).

## 🔧 Configuration

### Port Mapping

Access services from your local network:

| Service | Port | URL Example |
|---------|------|-------------|
| qBittorrent | 9802 | `http://192.168.1.100:9802` |
| Jackett | 9803 | `http://192.168.1.100:9803` |
| Sonarr | 9804 | `http://192.168.1.100:9804` |
| Prowlarr | 9805 | `http://192.168.1.100:9805` |
| FlareSolverr | 9806 | `http://192.168.1.100:9806` |
| Readarr | 9808 | `http://192.168.1.100:9808` |
| Radarr | 9809 | `http://192.168.1.100:9809` |
| Bazarr | 9810 | `http://192.168.1.100:9810` |

**Note:** Replace `192.168.1.100` with your Synology's IP address.

## 🔒 Security & Monitoring

### VPN Protection (via Gluetun)
- All download traffic routed through VPN tunnel
- Kill switch prevents leaks if VPN disconnects
- External IP verification
- Automatic VPN reconnection on failures

### VPN Sentinel Monitoring

This stack integrates **[VPN Sentinel](https://github.com/agigante80/VPNSentinel)** by [@agigante80](https://github.com/agigante80) for comprehensive VPN monitoring.

**What VPN Sentinel Provides:**
- **Client-Server Architecture**: Client monitors from inside VPN, server runs with real IP
- **Telegram Bot**: Interactive commands (`/status`, `/ping`, `/help`)
- **Real-time Alerts**: Immediate notifications when VPN connects/disconnects
- **DNS Leak Detection**: Compares VPN location with DNS resolver location
- **Location Tracking**: Shows current VPN server location and ISP
- **Connection History**: Tracks VPN uptime and connection status

**See [VPN Sentinel documentation](https://github.com/agigante80/VPNSentinel) for full details.**

### Example Telegram Notifications

**🔴 VPN Disconnection Alert:**
```
🚨 VPN CONNECTION LOST! 🚨
Client: my-media-stack
Time: 2025-01-12 15:30:15
External IP: 203.0.113.42 (Real IP detected!)
Location: United States, New York
ISP: Example ISP
DNS Servers: 8.8.8.8 (DNS leak detected!)
⚠️ Media services are now exposed!
```

**✅ VPN Restored Alert:**
```
✅ VPN CONNECTION RESTORED ✅
Client: my-media-stack
Time: 2025-01-12 15:35:22  
External IP: 185.72.199.129 (VPN IP)
Location: Poland, Toruń
ISP: PrivateVPN
DNS Servers: VPN DNS (secure)
🛡️ All traffic now protected
```

**📊 Status Check (via `/status` command):**
```
📊 VPN STATUS REPORT 📊
Client: my-media-stack
Status: ✅ CONNECTED
External IP: 185.170.104.53
Location: Poland, Toruń
Connected since: 2 hours 15 minutes
Last check: 30 seconds ago
```

### Telegram Bot Commands

| Command | Description |
|---------|-------------|
| `/status` | Get current VPN status with IP and location |
| `/ping` | Test bot connectivity |
| `/help` | Show available commands |

### Network Isolation

- **Media Network**: All *arr apps and qBittorrent share Gluetun's network
- **Container Communication**: Services communicate via `localhost` (shared network namespace)
- **VPN Sentinel**: Optional monitoring runs in separate network spaces

## 🛠️ Advanced Usage

### Container Communication

Containers sharing Gluetun's network (via `network_mode: "service:gluetun"`) communicate via `localhost`:

```bash
# From qBittorrent UI, configure:
Jackett URL: http://localhost:9117
Prowlarr URL: http://localhost:9696

# From Sonarr/Radarr, configure:
qBittorrent: http://localhost:8085
FlareSolverr: http://localhost:8191
```

### VPN Status Monitoring Commands

```bash
# Check current VPN IP
docker exec gluetun wget -qO- https://ipinfo.io/ip

# Get detailed location information
docker exec gluetun wget -qO- https://ipinfo.io/json

# DNS leak test (VPN country should match DNS country)
VPN_COUNTRY=$(docker exec gluetun wget -qO- https://ipinfo.io/json | grep country | cut -d'"' -f4)
DNS_COUNTRY=$(docker exec gluetun wget -qO- https://1.1.1.1/cdn-cgi/trace | grep '^loc=' | cut -d'=' -f2)
echo "VPN: $VPN_COUNTRY | DNS: $DNS_COUNTRY"

# View real-time VPN logs
docker logs gluetun --follow

# Check Gluetun health
docker exec gluetun wget -qO- http://localhost:9999/health
```

### VPN Sentinel Status

```bash
# View VPN Sentinel client logs (runs inside VPN)
docker logs vpn-sentinel-client --tail 20

# View VPN Sentinel server logs (external)
docker logs vpn-sentinel-server --tail 20

# Check both in real-time
docker compose logs -f vpn-sentinel-client vpn-sentinel-server
```

### Updating Containers

```bash
# Update all containers
docker compose pull
docker compose up -d

# Update specific container
docker compose pull gluetun
docker compose up -d gluetun

# Restart specific service
docker compose restart sonarr
```

**⚠️ Note on Gluetun Updates:** This repository pins Gluetun to `v3.40.4` for Synology compatibility. Before updating to newer versions, review the Synology compatibility section above.

## 🐛 Troubleshooting

### Quick Diagnostics

```bash
# Check container status
docker compose ps

# View logs for specific services
docker compose logs gluetun | grep -iE 'error|warn'
docker compose logs qbittorrent --tail 20

# Test VPN connectivity
docker exec gluetun wget -qO- https://ipinfo.io/ip

# Test container communication
docker exec sonarr curl -s http://localhost:9696  # Prowlarr
docker exec sonarr curl -s http://localhost:8085  # qBittorrent
```

### Common Issues

**1. VPN Not Connecting**
- Check VPN credentials in `.env`
- Verify `VPN_SERVICE_PROVIDER` is correct
- Check Gluetun logs: `docker logs gluetun`
- Try a different VPN server country

**2. Services Not Accessible**
- Verify containers are running: `docker compose ps`
- Check port conflicts: `netstat -tlnp | grep 980`
- Verify Synology firewall allows ports

**3. Container Communication Issues**
- Containers must use `localhost` (they share Gluetun's network)
- Use **internal ports**, not external: `http://localhost:8085` (not 9802)
- Check logs: `docker compose logs <service-name>`

**4. VPN Sentinel Not Sending Alerts**
- Verify Telegram bot token and chat ID in `.env`
- Test bot: Send message to your bot via Telegram
- Check logs: `docker logs vpn-sentinel-server`
- Verify VPN Sentinel server is reachable from client

**5. Gluetun Restart Loops (Synology)**
- **Symptom**: `netfilter query: netlink receive: invalid argument`
- **Cause**: Using Gluetun v3.41.0+ without sysctls
- **Solution**: Use v3.40.4 (default) or add sysctls (see Synology section)

### Container Restart Sequence

If services can't communicate:

```bash
# Restart VPN gateway first
docker compose restart gluetun
sleep 10

# Then restart dependent services
docker compose restart qbittorrent sonarr radarr

# Verify status
docker compose ps
```

### Full System Restart

```bash
# Stop all containers
docker compose down

# Start everything fresh
docker compose up -d

# Monitor startup
docker compose logs -f
```

### Getting Help

1. Check Gluetun logs: `docker logs gluetun --tail 50`
2. Check VPN Sentinel logs if using: `docker logs vpn-sentinel-client`
3. Verify `.env` configuration (especially VPN credentials)
4. Test VPN connectivity outside Docker first
5. Consult [Gluetun documentation](https://github.com/qdm12/gluetun/wiki)
6. Consult [VPN Sentinel documentation](https://github.com/agigante80/VPNSentinel)

## 📁 Directory Structure

```
VPN-media/                          # Repository root
├── compose.yaml                    # Docker Compose configuration
├── .env.example                    # Environment variables template
├── .gitignore                      # Git ignore patterns
├── README.md                       # This documentation
└── gluetun/                        # Gluetun VPN config (auto-created)
    └── PrivateVPN-*.ovpn          # Optional custom VPN configs
```

**Runtime Directories** (auto-created on Synology at `/volume1/docker/project/VPN-media/`):
```
qbittorrent/config/                 # qBittorrent settings and session
jackett/config/                     # Jackett indexer configurations
prowlarr/config/                    # Prowlarr settings
sonarr/config/                      # Sonarr TV configuration
radarr/config/                      # Radarr movie configuration  
readarr/config/                     # Readarr book configuration
bazarr/config/                      # Bazarr subtitle settings
```

**Note:** Config directories are excluded from git via `.gitignore`.

## 🔄 Updates & Maintenance

### Updating Containers

```bash
# Update all to latest versions
docker compose pull
docker compose up -d

# Update specific service
docker compose pull sonarr
docker compose up -d sonarr
```

**⚠️ Gluetun Version Pinning:**  
This repo pins Gluetun to `v3.40.4` for Synology compatibility. To update to v3.41.0+:
1. Review [Synology Compatibility](#-synology-nas-compatibility) section above
2. Add required sysctls to `compose.yaml`, OR
3. Stay on v3.40.4 (recommended for Synology)

### Backup Configuration

```bash
# Backup all service configurations
cd /volume1/docker/project/VPN-media
tar -czf backup-$(date +%Y%m%d).tar.gz */config/

# Restore from backup
tar -xzf backup-20260228.tar.gz
```

## 🔐 Synology-Specific Configuration

### Recommended Synology Settings

1. **Docker Package**: Install from Package Center
2. **SSH Access**: Enable for management
3. **User Permissions**: 
   - PUID: Typically `1026` for `admin` user on Synology
   - PGID: Typically `100` for `users` group
   - Get with: `id admin`

4. **Volume Structure**:
   ```
   /volume1/docker/project/       # Docker configurations
   /volume1/Media/                # Media library
   /volumeUSB2/usbshare/          # External USB (optional)
   ```

5. **Port Conflicts to Avoid**:
   - 5000, 5001 (DSM web interface)
   - 5800, 5801 (Surveillance Station)
   - This stack uses 9802-9810 (safe range)

6. **File Permissions**:
   ```bash
   # If you encounter permission issues:
   sudo chown -R admin:users /volume1/docker/project/VPN-media
   sudo chmod -R 755 /volume1/docker/project/VPN-media
   ```

### Why Gluetun v3.40.4 for Synology?

**Breaking Change:** Gluetun v3.41.0 introduced `netfilter` requirements that conflict with Synology's Docker implementation.

| Version | Release | Synology Status |
|---------|---------|-----------------|
| v3.40.4 | Dec 24, 2024 | ✅ Works perfectly |
| v3.41.0+ | Dec 25, 2024+ | ⚠️ Needs sysctls or breaks |

**Symptoms without fix:**
- Container restart loops
- `netfilter query: netlink receive: invalid argument`
- VPN never connects

**Options:**
1. **Use v3.40.4** (this repo's default) - Works out of box
2. **Add sysctls** for v3.41.0+ - Requires compose.yaml modification

This repository defaults to option 1 for maximum compatibility.

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

**Attribution:**
- This is a Docker Compose configuration integrating existing open-source tools
- [**VPN Sentinel**](https://github.com/agigante80/VPNSentinel) by [@agigante80](https://github.com/agigante80)
- [**Gluetun**](https://github.com/qdm12/gluetun) by [@qdm12](https://github.com/qdm12)  
- [**Servarr Apps**](https://servarr.com/) by various contributors
- Each component maintains its own license

## 🤝 Contributing

Contributions welcome! Please submit Pull Requests for:
- Synology-specific improvements or fixes
- Documentation enhancements
- Configuration optimizations
- Bug fixes

**Not accepting:**
- Alternative architectures (this is intentionally simple)
- Bundled media servers (keep it agnostic)
- Extensive custom scripts (use existing tools)

## ⚠️ Legal Disclaimer

**This is an EDUCATIONAL PROJECT demonstrating Docker Compose configuration and VPN integration.**

### 📚 Educational Purpose
This repository demonstrates:
- Docker Compose orchestration
- VPN integration patterns (`network_mode: service`)
- Synology NAS Docker deployment
- Integration of open-source monitoring tools

### 🚨 User Responsibility
- **Review Local Laws**: Copyright, VPN usage, and content laws vary by jurisdiction
- **Compliance**: Users are solely responsible for ensuring legal compliance
- **VPN Regulations**: Some countries restrict or prohibit VPN usage
- **Content Sources**: Ensure all content and indexers comply with local laws

### 🛡️ Liability
The authors and contributors are not responsible for:
- Any misuse of this configuration or software
- Legal consequences from improper use
- Violation of local laws or terms of service
- Any damages arising from use of this setup

**Use at your own risk. Ensure full compliance with all applicable laws and regulations.**

---

## 🔗 Quick Links

- **[Gluetun Documentation](https://github.com/qdm12/gluetun/wiki)**
- **[VPN Sentinel Project](https://github.com/agigante80/VPNSentinel)**
- **[Servarr Wiki](https://wiki.servarr.com/)**
- **[qBittorrent Documentation](https://github.com/qbittorrent/qBittorrent/wiki)**

## 💡 Tips & Best Practices

1. **Always use VPN**: Never run download clients without VPN protection
2. **Monitor regularly**: Check VPN Sentinel alerts to ensure protection
3. **Update carefully**: Test updates in non-production environment first
4. **Backup configs**: Regular backups of service configurations
5. **Use Trakt**: Integrate Trakt with Sonarr/Radarr for content discovery
6. **Check logs**: When troubleshooting, start with Gluetun logs
7. **Synology**: Stick with v3.40.4 unless you need v3.41.0+ features

---

**Made for Synology NAS users who want a simple, working VPN-protected media automation setup.**
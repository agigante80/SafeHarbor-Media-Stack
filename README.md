# SafeHarbor Media Stack 🎬🔒
*Secure Docker Stack for VPN-Protected Media Management*

A complete Docker Compose setup for automated media management with VPN protection, featuring popular *arr applications, qBittorrent, and professional VPN monitoring powered by [VPNSentinel](https://github.com/agigante80/VPNSentinel).

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue)](https://docs.docker.com/compose/)
[![VPN](https://img.shields.io/badge/VPN-Gluetun-green)](https://github.com/qdm12/gluetun)
[![VPNSentinel](https://img.shields.io/badge/Monitoring-VPNSentinel-blue)](https://github.com/agigante80/VPNSentinel)

## 🚨 **UNIQUE: VPNSentinel Monitoring System**

> **🔥 What sets SafeHarbor apart:** Integration with **[VPNSentinel](https://github.com/agigante80/VPNSentinel)** - a professional open-source VPN monitoring solution with client/server architecture + live Telegram notifications that creates a bulletproof system detecting VPN issues and instantly alerting you.

### 🎯 **VPNSentinel: Professional VPN Monitoring**

**⚡ The SafeHarbor Advantage - Powered by VPNSentinel:**

**🔧 VPNSentinel Client/Server Architecture:**
- **Inside VPN monitoring** - Client monitors connection health from within protected network
- **Outside VPN reporting** - Server receives status reports from real IP network
- **Continuous health checks** - Configurable interval checks with instant failure detection
- **DNS leak detection** - Verifies your real IP is never exposed
- **Geolocation tracking** - Monitors VPN server location changes
- **Web dashboard** - Visual monitoring interface with real-time status

**📱 Telegram Integration:**
- **Instant mobile alerts** - Get notified on your phone within seconds of any issue
- **Interactive bot commands** - `/ping`, `/status`, `/help` for remote monitoring
- **Rich notifications** - Detailed VPN status, location, and DNS leak information
- **Real-time status updates** - Continuous reporting directly to your Telegram

**🚀 Why VPNSentinel is Unstoppable:**
- **Professional monitoring** → **Instant notifications** → **Peace of mind**
- **Technical precision** + **Human-friendly alerts** = **Complete solution**
- **Standalone project** - Can be deployed with ANY Docker VPN stack
- **Open source** - Full transparency and community-driven development

**🛡️ Advanced Protection & Notification Features:**
- **DNS leak detection** with instant Telegram alerts
- **Real-time monitoring** - Configurable interval checks with live notifications
- **VPN failure alerts** - Immediate Telegram messages when connection drops
- **IP change notifications** - Get notified instantly if your VPN IP changes with geolocation data
- **Interactive commands** - Check system status remotely via Telegram
- **Web dashboard** - Visual monitoring at http://your-server:8080
- **API endpoints** - `/status`, `/health` for integration with external systems
- **Client versioning** - Track monitoring client versions

**💡 Why VPNSentinel + Telegram is Game-Changing:**
Most VPN stacks fail silently, leaving you exposed without knowing. SafeHarbor's **VPNSentinel integration** means:

**🔍 VPNSentinel Client detects:**
- VPN connection failures within the protected network
- DNS leaks that expose your real IP
- IP address and geolocation changes
- Container networking issues
- VPN server location changes

**📱 Telegram Bot instantly alerts you:**
- VPN connection drops (get alert within seconds)
- IP address changes unexpectedly (real-time notification with location data)  
- DNS queries leak your real location (immediate warning with DNS server details)
- Client disconnections (know when monitoring stops)
- Server startup notifications

**🎯 Result: Professional monitoring + Instant notifications = Never be caught off-guard**

**Learn more:** [VPNSentinel Documentation](https://github.com/agigante80/VPNSentinel)

## ✨ Complete Feature Set

- **🔒 VPN Protection**: All media traffic routed through Gluetun VPN (25+ providers supported)
- **📺 Complete Media Stack**: Sonarr, Radarr, Readarr, Prowlarr, Jackett, Bazarr, qBittorrent
- **🤖 VPNSentinel Monitoring**: Professional VPN monitoring with Telegram notifications and DNS leak detection
- **🌐 Web Dashboard**: Visual monitoring interface with real-time client status
- **🏠 Synology Compatible**: Tested and working on Synology NAS systems
- **🎯 Media Agnostic**: Works with any media server (Plex, Jellyfin, Emby)

## 🚀 Quick Start

### Prerequisites
- Docker and Docker Compose
- VPN provider account (PrivateVPN supported out-of-the-box)
- Telegram bot token (optional, for VPNSentinel monitoring)

### 1. Setup
```bash
# Clone repository
git clone https://github.com/agigante80/SafeHarbor-Media-Stack.git
cd SafeHarbor-Media-Stack

# Configure environment
cp .env.example .env
nano .env  # Add your VPN credentials and VPNSentinel settings

# Create directories and start
mkdir -p gluetun qbittorrent/config jackett/{config,downloads}
mkdir -p prowlarr/config sonarr/config radarr/config readarr/config bazarr/config
docker compose up -d
```

### 2. Access Services
| Service | Port | URL | Purpose |
|---------|------|-----|---------|
| qBittorrent | 9802 | http://your-server:9802 | Download client |
| Sonarr | 9804 | http://your-server:9804 | TV shows |
| Radarr | 9809 | http://your-server:9809 | Movies |
| Prowlarr | 9805 | http://your-server:9805 | Indexer manager |
| Jackett | 9803 | http://your-server:9803 | Indexer proxy |
| **VPNSentinel API** | 5421 | http://your-server:5421 | Monitoring API |
| **VPNSentinel Dashboard** | 8080 | http://your-server:8080 | Web dashboard |

**Default qBittorrent login**: `admin` / `adminadmin` (change immediately!)

## 🔧 Basic Configuration

### Environment Variables (.env)
```bash
# VPN Settings
VPN_SERVICE_PROVIDER=privatevpn
VPN_USER=your_vpn_username
VPN_PASSWORD=your_vpn_password
SERVER_COUNTRIES=Switzerland,Netherlands

# Paths
VOLUME_DOCKER_PROJECT=/path/to/docker/project
VOLUME_DOWNLOADS=/path/to/downloads
VOLUME_MEDIA=/path/to/media

# User IDs
PUID_MEDIA=1000
PGID_MEDIA=1000

# VPNSentinel Monitoring (highly recommended)
VPN_SENTINEL_API_KEY=your_secure_api_key_here
VPN_SENTINEL_CLIENT_ID=safeharbor-media-stack
VPN_SENTINEL_CHECK_INTERVAL=300  # 5 minutes
VPN_SENTINEL_ALERT_THRESHOLD_MINUTES=15
VPN_SENTINEL_CHECK_INTERVAL_MINUTES=5

# Telegram Notifications
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

### Telegram Bot Setup (Recommended for VPNSentinel)
1. Message [@BotFather](https://t.me/botfather) → `/newbot`
2. Get your Chat ID from [@userinfobot](https://t.me/userinfobot)
3. Add tokens to `.env` file
4. Restart stack: `docker compose restart vpn-sentinel-server`

**Bot Commands**: `/ping`, `/status`, `/help`

**VPNSentinel Features:**
- 📊 Web dashboard at `http://your-server:8080`
- 🏥 Health check endpoint at `http://your-server:8081/health`
- 📡 API status at `http://your-server:5421/api/v1/status`

## 🔧 **Standalone VPNSentinel Deployment**

> **Use VPNSentinel monitoring with ANY Docker VPN setup!**

VPNSentinel can be deployed independently to monitor **any** VPN-protected container stack. It's a **completely separate project** that SafeHarbor integrates with.

### Quick Standalone Setup
```yaml
version: '3.8'
services:
  # Your existing VPN container (gluetun, transmission-vpn, etc.)
  your-vpn-container:
    # ... your VPN config ...

  # Add VPNSentinel monitoring
  vpn-sentinel-server:
    image: agigante80/vpn-sentinel-server:latest
    environment:
      - TELEGRAM_BOT_TOKEN=your_bot_token
      - VPN_SENTINEL_TELEGRAM_CHAT_ID=your_chat_id
      - VPN_SENTINEL_API_KEY=your-secure-key
    ports:
      - "5421:5000"  # API
      - "8080:8080"  # Dashboard
      - "8081:8081"  # Health

  vpn-sentinel-client:
    image: agigante80/vpn-sentinel-client:latest
    environment:
      - VPN_SENTINEL_SERVER_URL=http://vpn-sentinel-server:5000
      - VPN_SENTINEL_API_KEY=your-secure-key
      - VPN_SENTINEL_CLIENT_ID=my-vpn-monitor
    network_mode: "container:your-vpn-container"  # Monitor VPN container
    depends_on:
      - your-vpn-container
```

### Integration Benefits - The Power of VPNSentinel
- **Complete monitoring ecosystem** - Professional client/server architecture
- **Drop-in deployment** for existing setups with zero configuration changes
- **Universal VPN support** - works with any VPN container (gluetun, transmission-vpn, etc.)
- **Dual-layer protection** - Technical monitoring + human alerts
- **Instant mobile notifications** - Never miss a VPN failure
- **Interactive remote control** - `/ping`, `/status`, `/help` commands
- **Web dashboard** - Visual monitoring interface
- **Lightweight yet powerful** - minimal resources, maximum protection
- **Production ready** - battle-tested standalone project

**Full VPNSentinel Documentation:** https://github.com/agigante80/VPNSentinel

## 🛡️ VPN & Security

### SafeHarbor Architecture - VPNSentinel Integration
```
┌─────────────────────────────────────────────────────────────┐
│                    Host Network                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │            🔒 Gluetun VPN Gateway (Protected)           ││
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        ││
│  │  │qBittorr │ │ Sonarr  │ │ Radarr  │ │ Jackett │ ...    ││
│  │  │   ent   │ │         │ │         │ │         │        ││
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘        ││
│  │                                                         ││
│  │  ┌─────────────────────────────────────────────────┐    ││
│  │  │    🚨 VPNSentinel CLIENT (Professional Monitor) │    ││
│  │  │  • DNS Leak Detection  • IP Change Monitoring   │    ││
│  │  │  • Connection Testing  • Geolocation Tracking   │    ││
│  │  └─────────────────────────────────────────────────┘    ││
│  └─────────────────────────────────────────────────────────┘│
│                           │                                 │
│                ⚡ SECURE REPORTING TUNNEL                   │
│                           │                                 │
│  ┌─────────────────────────────────────────────────────────┐│
│  │  🌐 VPNSentinel SERVER (Real IP Network) 🌐             ││
│  │                                                         ││
│  │  ┌─────────────────┐    ┌─────────────────┐             ││
│  │  │  📱 Telegram    │    │  📊 Web Dashboard│             ││
│  │  │  Bot Server     │◄───┤  Status API     │             ││
│  │  │  (Instant       │    │  /status /health │             ││
│  │  │   Alerts)       │    └─────────────────┘             ││
│  │  └─────────────────┘                                    ││
│  │           ▲                                             ││
│  │           │ Status Reports Every 5min                   ││
│  │           │ + Instant Failure Alerts                    ││
│  └───────────┼─────────────────────────────────────────────┘│
│               │                                             │
└───────────────┼─────────────────────────────────────────────┘
                │ Internet (Real IP)
                └─ Receives health reports from VPN network
```

**🔥 SafeHarbor's Unique VPNSentinel Architecture:**

**🔧 VPNSentinel System (Technical Layer):**
- **Inside VPN**: Client monitors connection health from protected environment
- **Outside VPN**: Server receives reports and coordinates responses
- **Continuous Detection**: DNS leak protection, IP monitoring, geolocation tracking
- **API Endpoints**: `/status`, `/health`, `/api/v1/*` for external system integration
- **Web Dashboard**: Visual monitoring at port 8080

**📱 Telegram System (Human Layer):**
- **Instant Mobile Notifications**: Get alerts directly on your phone within seconds
- **Interactive Commands**: Remote control via `/ping`, `/status`, `/help`
- **Rich Notifications**: Detailed VPN status with location and DNS information
- **Real-time Updates**: Continuous status reporting and failure notifications

**⚡ The Magic Happens When They Work Together:**
- **VPNSentinel detects** → **Telegram notifies** → **You respond instantly**
- **Technical precision** + **Human accessibility** = **Bulletproof protection**
- **Never miss a failure** - Professional monitoring with mobile alerts

## 🐛 Quick Troubleshooting

### VPN Issues
```bash
# Check VPN status
docker logs gluetun --tail 20
docker exec gluetun wget -qO- https://ipinfo.io/ip

# Restart if needed
docker compose restart gluetun
```

### VPNSentinel Issues
```bash
# Check VPNSentinel client logs
docker logs vpn-sentinel-client --tail 50

# Check VPNSentinel server logs  
docker logs vpn-sentinel-server --tail 50

# Test API connection
curl http://localhost:5421/api/v1/health

# View web dashboard
open http://localhost:8080
```

### Container Issues
```bash
# Check all services
docker compose ps

# Restart specific service
docker compose restart sonarr

# View real-time logs
docker compose logs -f vpn-sentinel-client
```

### Common Problems
- **VPN not connecting**: Check credentials in `.env`
- **Services unreachable**: Verify container status with `docker compose ps`
- **Port conflicts**: Check if ports 9802-9811, 5421, 8080, 8081 are available
- **VPNSentinel not reporting**: Verify API key matches between client and server

## 📚 Documentation

For detailed documentation, troubleshooting, and advanced configuration:

### 📖 **Wiki Documentation**
- **[Installation Guide](../../wiki/Installation-Guide)** - Detailed setup instructions
- **[Configuration](../../wiki/Configuration)** - Advanced settings and customization
- **[Telegram Bot](../../wiki/Telegram-Bot)** - Complete bot setup and commands
- **[Troubleshooting](../../wiki/Troubleshooting)** - Real-world scenarios and solutions
- **[VPN Monitoring](../../wiki/VPN-Monitoring)** - VPNSentinel integration guide
- **[Architecture](../../wiki/Architecture)** - Technical deep-dive and networking
- **[Synology Guide](../../wiki/Synology-Guide)** - NAS-specific instructions
- **[FAQ](../../wiki/FAQ)** - Frequently asked questions
- **[Updates](../../wiki/Updates)** - Keeping your stack current

### 🔗 **External Resources**
- **[VPNSentinel Project](https://github.com/agigante80/VPNSentinel)** - Standalone monitoring solution
- **[VPNSentinel Documentation](https://github.com/agigante80/VPNSentinel#readme)** - Full monitoring documentation
- **[Gluetun Documentation](https://github.com/qdm12/gluetun/wiki)** - VPN container documentation

## 🥊 **SafeHarbor vs. Other Media Stacks**

| Feature | SafeHarbor | Other Stacks | Advantage |
|---------|------------|--------------|-----------|
| **VPNSentinel Integration** | ✅ Professional monitoring system | ❌ No monitoring or basic scripts | **Enterprise-grade protection** |
| **Live Mobile Notifications** | ✅ Instant Telegram alerts | ❌ Silent failures | **Never miss VPN issues** |
| **Interactive Bot Commands** | ✅ `/ping`, `/status`, `/help` | ❌ No remote control | **Monitor & control anywhere** |
| **Web Dashboard** | ✅ Visual monitoring interface | ❌ No visual monitoring | **Easy status overview** |
| **DNS Leak Protection** | ✅ Active monitoring + alerts | ❌ Hope for the best | **Verified IP protection** |
| **Geolocation Tracking** | ✅ VPN server location monitoring | ❌ No location tracking | **Know your VPN location** |
| **Dual-Network Design** | ✅ Inside + outside VPN checks | ❌ Single network only | **Redundant protection** |
| **API Integration** | ✅ Full REST API | ❌ Limited or no API | **External system integration** |
| **Open Source Monitoring** | ✅ VPNSentinel standalone project | ❌ Proprietary or none | **Community-driven security** |

### 🌊 Alternative Projects
- [navilg/media-stack](https://github.com/navilg/media-stack) - Complete media server with Plex
- [DonMcD/ultimate-plex-stack](https://github.com/DonMcD/ultimate-plex-stack) - Ultimate Plex stack
- **SafeHarbor difference**: These stacks focus on media management. SafeHarbor adds **VPNSentinel integration** - a professional open-source VPN monitoring system with instant mobile alerts that no other stack offers.

## ⚠️ Important Notice

**This is an EDUCATIONAL PROJECT** for demonstrating Docker networking and VPN integration concepts.

- **Review local laws** regarding VPN usage and content downloading
- **Use responsibly** and ensure compliance with applicable regulations
- **Support content creators** and legal distribution platforms

## 📝 License & Contributing

This project is licensed under the [MIT License](LICENSE).

Contributions welcome! Please feel free to submit Pull Requests or report issues.

**Related Projects:**
- **[VPNSentinel](https://github.com/agigante80/VPNSentinel)** - The monitoring system powering SafeHarbor

---

**🔗 Quick Links:**
[Installation](../../wiki/Installation-Guide) | [Configuration](../../wiki/Configuration) | [VPNSentinel](https://github.com/agigante80/VPNSentinel) | [Troubleshooting](../../wiki/Troubleshooting) | [Issues](../../issues) | [Discussions](../../discussions)

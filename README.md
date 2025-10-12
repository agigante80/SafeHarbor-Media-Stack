# SafeHarbor Media Stack 🎬🔒
*Secure Docker Stack for VPN-Protected Media Management*

A complete Docker Compose setu## 🔧 **Standalone Keepalive + Telegram Deployment**

> **Add SafeHarbor's dual monitoring system (keepalive + Telegram) to ANY Docker VPN setup!**

The complete **keepalive client/server + Telegram notification system** can be deployed independently to monitor **any** VPN-protected container stack: automated media management with VPN protection, featuring popular *arr applications, qBittorrent, and intelligent monitoring with Telegram notifications.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue)](https://docs.docker.com/compose/)
[![VPN](https://img.shields.io/badge/VPN-Gluetun-green)](https://github.com/qdm12/gluetun)

## 🚨 **UNIQUE: Live Telegram Monitoring & Notification System**

> **🔥 What sets SafeHarbor apart:** Our **unique combination of keepalive client/server monitoring + live Telegram notifications** creates a bulletproof system that detects VPN issues and instantly alerts you - something no other media stack offers.

### 🎯 **Keepalive + Telegram: The Powerful Combination**

**⚡ The SafeHarbor Advantage - Two Systems Working as One:**

**� Keepalive Client/Server System:**
- **Inside VPN monitoring** - Client monitors connection health from within protected network
- **Outside VPN reporting** - Server receives status reports from real IP network
- **Continuous health checks** - 5-minute intervals with instant failure detection
- **DNS leak detection** - Verifies your real IP is never exposed

**📱 Telegram Notification System:**
- **Instant mobile alerts** - Get notified on your phone within seconds of any issue
- **Interactive bot commands** - `/ping`, `/status`, `/help` for remote monitoring
- **Smart log analysis** - Bot analyzes container logs and provides meaningful alerts
- **Real-time status updates** - Continuous reporting directly to your Telegram

**🚀 Why The Combination is Unstoppable:**
- **Keepalive detects issues** → **Telegram instantly notifies you**
- **Technical monitoring** + **Human-friendly notifications** = **Complete solution**
- **Works independently** - Can be deployed with ANY Docker VPN stack

**🛡️ Advanced Protection & Notification Features:**
- **DNS leak detection** with instant Telegram alerts
- **Real-time monitoring** - 5-minute interval checks with live notifications
- **VPN failure alerts** - Immediate Telegram messages when connection drops
- **IP change notifications** - Get notified instantly if your VPN IP changes
- **Interactive commands** - Check system status remotely via Telegram
- **Log analysis alerts** - Smart detection of issues from container logs
- **API endpoints** - `/status`, `/health` for integration with external systems

**💡 Why The Keepalive + Telegram Combination is Game-Changing:**
Most VPN stacks fail silently, leaving you exposed without knowing. SafeHarbor's **dual-system approach** means:

**🔍 Keepalive Client/Server detects:**
- VPN connection failures within the protected network
- DNS leaks that expose your real IP
- Container networking issues
- Performance degradation

**📱 Telegram System instantly alerts you:**
- VPN connection drops (get alert within seconds)
- IP address changes unexpectedly (real-time notification)  
- DNS queries leak your real location (immediate warning)
- Container networking fails (instant troubleshooting info)
- Downloads complete or fail (progress updates)
- System resources run low (proactive alerts)

**🎯 Result: Technical monitoring + Human notifications = Never be caught off-guard**

## ✨ Complete Feature Set

- **🔒 VPN Protection**: All media traffic routed through Gluetun VPN (25+ providers supported)
- **📺 Complete Media Stack**: Sonarr, Radarr, Readarr, Prowlarr, Jackett, Bazarr, qBittorrent
- **🤖 Smart Monitoring**: Real-time VPN status with Telegram notifications and DNS leak detection
- **🏠 Synology Compatible**: Tested and working on Synology NAS systems
- **🎯 Media Agnostic**: Works with any media server (Plex, Jellyfin, Emby)

## 🚀 Quick Start

### Prerequisites
- Docker and Docker Compose
- VPN provider account (PrivateVPN supported out-of-the-box)
- Telegram bot token (optional, for monitoring)

### 1. Setup
```bash
# Clone repository
git clone https://github.com/agigante80/SafeHarbor-Media-Stack.git
cd SafeHarbor-Media-Stack

# Configure environment
cp .env.example .env
nano .env  # Add your VPN credentials and Telegram settings

# Create directories and start
mkdir -p gluetun qbittorrent/config jackett/{config,downloads}
mkdir -p prowlarr/config sonarr/config radarr/config readarr/config bazarr/config
docker compose up -d
```

### 2. Access Services
| Service | Port | URL |
|---------|------|-----|
| qBittorrent | 9802 | http://your-server:9802 |
| Sonarr | 9804 | http://your-server:9804 |
| Radarr | 9809 | http://your-server:9809 |
| Prowlarr | 9805 | http://your-server:9805 |
| Jackett | 9803 | http://your-server:9803 |

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

# Telegram (optional)
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

### Telegram Bot Setup (Optional)
1. Message [@BotFather](https://t.me/botfather) → `/newbot`
2. Get your Chat ID from [@userinfobot](https://t.me/userinfobot)
3. Add tokens to `.env` file
4. Restart stack: `docker compose restart keepalive-server`

**Bot Commands**: `/ping`, `/status`, `/help`

## � **Standalone Keepalive Deployment**

> **Use SafeHarbor's monitoring system with ANY Docker VPN setup!**

The keepalive client/server can be deployed independently to monitor **any** VPN-protected container stack:

### Quick Standalone Setup
```bash
# Option 1: Use only the monitoring components
version: '3.8'
services:
  # Your existing VPN container (gluetun, transmission-vpn, etc.)
  your-vpn-container:
    # ... your VPN config ...

  # Add SafeHarbor's dual monitoring system
  # Part 1: Keepalive Server (receives reports + sends Telegram alerts)
  keepalive-server:
    image: safeharbor/keepalive-server:latest
    environment:
      - TELEGRAM_BOT_TOKEN=your_bot_token      # Telegram integration
      - TELEGRAM_CHAT_ID=your_chat_id          # Your notifications
      - KEEPALIVE_API_KEY=your-secure-key      # Secure communication
    ports:
      - "5421:5421"

  # Part 2: Keepalive Client (monitors from inside VPN)
  keepalive-client:
    image: safeharbor/keepalive-client:latest
    environment:
      - KEEPALIVE_SERVER_URL=http://keepalive-server:5421
      - KEEPALIVE_API_KEY=your-secure-key
    network_mode: "container:your-vpn-container"  # Monitor VPN from inside
    depends_on:
      - your-vpn-container
```

### Integration Benefits - The Power of Both Systems
- **Complete monitoring ecosystem** - Keepalive detection + Telegram notifications
- **Drop-in deployment** for existing setups with zero configuration changes
- **Universal VPN support** - works with any VPN container (gluetun, transmission-vpn, etc.)
- **Dual-layer protection** - Technical monitoring + human alerts
- **Instant mobile notifications** - Never miss a VPN failure
- **Interactive remote control** - `/ping`, `/status`, `/help` commands
- **Lightweight yet powerful** - minimal resources, maximum protection
- **Production ready** - battle-tested combination in SafeHarbor deployments

## �🛡️ VPN & Security

### SafeHarbor Architecture - Keepalive + Telegram Working Together
```
┌──────────────────────────────────────────────────────────────┐
│                    Host Network                              │
│  ┌──────────────────────────────────────────────────────────┐│
│  │            🔒 Gluetun VPN Gateway (Protected)            ││
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐         ││
│  │  │qBittorr │ │ Sonarr  │ │ Radarr  │ │ Jackett │ ...     ││
│  │  │   ent   │ │         │ │         │ │         │         ││
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘         ││
│  │                                                          ││
│  │  ┌──────────────────────────────────────────────────┐    ││
│  │  │    🚨 KEEPALIVE CLIENT (SafeHarbor Secret Sauce) │    ││
│  │  │  • DNS Leak Detection  • IP Change Monitoring    │    ││
│  │  │  • Connection Testing  • Health Status Reports   │    ││
│  │  └──────────────────────────────────────────────────┘    ││
│  └──────────────────────────────────────────────────────────┘│
│                           │                                  │
│                ⚡ SECURE REPORTING TUNNEL                     │
│                           │                                  │
│  ┌──────────────────────────────────────────────────────────┐│
│  │  🌐 KEEPALIVE SERVER (Real IP Network) 🌐                ││
│  │                                                          ││
│  │  ┌─────────────────┐    ┌──────────────────┐             ││
│  │  │  📱 Telegram    │    │  📊 Status API   │             ││
│  │  │  Bot Server     │◄───┤  /status /health │             ││
│  │  │  (Instant       │    │  /api/vpn-check  │             ││
│  │  │   Alerts)       │    └──────────────────┘             ││
│  │  └─────────────────┘                                     ││
│  │           ▲                                              ││
│  │           │ Status Reports Every 5min                    ││
│  │           │ + Instant Failure Alerts                     ││
│  └───────────┼──────────────────────────────────────────────┘│
│              │                                               │
└──────────────┼───────────────────────────────────────────────┘
               │ Internet (Real IP)
               └─ Receives health reports from VPN network
```

**🔥 SafeHarbor's Unique Dual-System Architecture:**

**🔧 Keepalive System (Technical Layer):**
- **Inside VPN**: Client monitors connection health from protected environment
- **Outside VPN**: Server receives reports and coordinates responses
- **Continuous Detection**: DNS leak protection, IP monitoring, connection testing
- **API Endpoints**: `/status`, `/health` for external system integration

**📱 Telegram System (Human Layer):**
- **Instant Mobile Notifications**: Get alerts directly on your phone within seconds
- **Interactive Commands**: Remote control via `/ping`, `/status`, `/help`
- **Smart Log Analysis**: Bot analyzes container logs and provides meaningful alerts
- **Real-time Updates**: Continuous status reporting and failure notifications

**⚡ The Magic Happens When They Work Together:**
- **Keepalive detects** → **Telegram notifies** → **You respond instantly**
- **Technical precision** + **Human accessibility** = **Bulletproof protection**
- **Never miss a failure** - dual-layer monitoring with mobile alerts

## 🐛 Quick Troubleshooting

### VPN Issues
```bash
# Check VPN status
docker logs gluetun --tail 20
docker exec gluetun wget -qO- https://ipinfo.io/ip

# Restart if needed
docker compose restart gluetun
```

### Container Issues
```bash
# Check all services
docker compose ps

# Restart specific service
docker compose restart sonarr

# View logs
docker compose logs -f keepalive-client
```

### Common Problems
- **VPN not connecting**: Check credentials in `.env`
- **Services unreachable**: Verify container status with `docker compose ps`
- **Port conflicts**: Check if ports 9802-9811 are available

## 📚 Documentation

For detailed documentation, troubleshooting, and advanced configuration:

### 📖 [**Wiki Documentation**](../../wiki)
- **[Installation Guide](../../wiki/Installation-Guide)** - Detailed setup instructions
- **[Configuration](../../wiki/Configuration)** - Advanced settings and customization
- **[Telegram Bot](../../wiki/Telegram-Bot)** - Complete bot setup and commands
- **[Troubleshooting](../../wiki/Troubleshooting)** - Real-world scenarios and solutions
- **[VPN Monitoring](../../wiki/VPN-Monitoring)** - DNS leak detection and status tracking
- **[Architecture](../../wiki/Architecture)** - Technical deep-dive and networking
- **[Synology Guide](../../wiki/Synology-Guide)** - NAS-specific instructions

## 🥊 **SafeHarbor vs. Other Media Stacks**

| Feature | SafeHarbor | Other Stacks | Advantage |
|---------|------------|--------------|-----------|
| **Keepalive + Telegram Combo** | ✅ Dual-system monitoring | ❌ No integrated solution | **Technical detection + Human alerts** |
| **Live Mobile Notifications** | ✅ Instant Telegram alerts | ❌ Silent failures | **Never miss VPN issues** |
| **Interactive Bot Commands** | ✅ `/ping`, `/status`, `/help` | ❌ No remote control | **Monitor & control anywhere** |
| **Dual-Network Monitoring** | ✅ Inside + outside VPN checks | ❌ Single network only | **Redundant failure detection** |
| **DNS Leak Protection** | ✅ Active monitoring + alerts | ❌ Hope for the best | **Verified IP protection** |
| **Smart Log Analysis** | ✅ Bot analyzes container logs | ❌ Manual log checking | **Proactive issue detection** |
| **Complete Standalone System** | ✅ Both systems work independently | ❌ All-or-nothing approach | **Flexible deployment** |
| **API Integration** | ✅ `/status`, `/health`, `/api/*` | ❌ Limited monitoring | **External system integration** |
| **Instant Failure Response** | ✅ Detect → Alert → Act pipeline | ❌ Manual discovery | **Immediate incident response** |

### 🌊 Alternative Projects
- [navilg/media-stack](https://github.com/navilg/media-stack) - Complete media server with Plex
- [DonMcD/ultimate-plex-stack](https://github.com/DonMcD/ultimate-plex-stack) - Ultimate Plex stack
- **SafeHarbor difference**: These stacks focus on media management. SafeHarbor adds a **revolutionary keepalive + Telegram notification system** that creates bulletproof VPN monitoring with instant mobile alerts - a combination no other stack offers.

## ⚠️ Important Notice

**This is an EDUCATIONAL PROJECT** for demonstrating Docker networking and VPN integration concepts.

- **Review local laws** regarding VPN usage and content downloading
- **Use responsibly** and ensure compliance with applicable regulations
- **Support content creators** and legal distribution platforms

## 📝 License & Contributing

This project is licensed under the [MIT License](LICENSE).

Contributions welcome! Please feel free to submit Pull Requests or report issues.

---

**🔗 Quick Links:**
[Installation](../../wiki/Installation-Guide) | [Configuration](../../wiki/Configuration) | [Troubleshooting](../../wiki/Troubleshooting) | [Issues](../../issues) | [Discussions](../../discussions)
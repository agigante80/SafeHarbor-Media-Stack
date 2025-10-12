# SafeHarbor Media Stack 🎬🔒
*Secure Docker Stack for VPN-Protected Media Management*

A complete Docker Compose setup for automated media management with VPN protection, featuring popular *arr applications, qBittorrent, and intelligent monitoring with Telegram notifications.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue)](https://docs.docker.com/compose/)
[![VPN](https://img.shields.io/badge/VPN-Gluetun-green)](https://github.com/qdm12/gluetun)

## ✨ Key Features

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

## 🛡️ VPN & Security

### How It Works
```
┌─────────────────────────────────────────────────────────────┐
│                    Host Network                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │                 Gluetun VPN Gateway                     ││
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        ││
│  │  │qBittorr │ │ Sonarr  │ │ Radarr  │ │ Jackett │ ...    ││
│  │  │   ent   │ │         │ │         │ │         │        ││
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘        ││
│  │                                                         ││
│  │  ┌─────────────────────────────────────────────────┐    ││
│  │  │              Keepalive Client                   │    ││
│  │  │            (VPN Status Monitor)                 │    ││
│  │  └─────────────────────────────────────────────────┘    ││
│  └─────────────────────────────────────────────────────────┘│
│                           │                                 │
│                           │ Internet (via VPN)              │
│                           │                                 │
│                           │  ┌─────────────────────────┐    │
│                           │  │     Keepalive Server    │    │
│                           └─►│  (Real IP + Telegram)   │    │
│                              │     Network Monitor     │    │
│                              └─────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

- **All media containers** route through VPN
- **Monitoring system** tracks VPN status and detects DNS leaks
- **Automatic alerts** via Telegram when VPN fails or IP changes

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

## 🌊 Alternative Projects
- [navilg/media-stack](https://github.com/navilg/media-stack) - Complete media server with Plex
- [DonMcD/ultimate-plex-stack](https://github.com/DonMcD/ultimate-plex-stack) - Ultimate Plex stack

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
# SafeHarbor Media Stack 🎬🔒
*Secure Docker Stack for VPN-Protected Media Management*

A complete Docker Compose setup for automated media management with VPN protection, featuring popular *arr applications, qBittorrent, and intelligent monitoring with Telegram notifications of VPN networking status.

## 🌊 Alternative Names
- **SafeHarbor Media Stack** - A secure harbor for your media services
- **MediaVault Stack** - Your protected media vault in the cloud

## 🔗 Related Projects
This is yet another media stack project, similar to:
- [**navilg/media-stack**](https://github.com/navilg/media-stack) - Complete media server stack with Plex
- [**DonMcD/ultimate-plex-stack**](https://github.com/DonMcD/ultimate-plex-stack) - Ultimate Plex media server stack

## ✨ Features

- **🔒 VPN Protection**: All media traffic routed through VPN (Gluetun)
- **📺 Complete Media Stack**: Sonarr, Radarr, Readarr, Prowlarr, Jackett, Bazarr
- **⬬ BitTorrent Client**: qBittorrent with WebUI
- **🤖 Telegram Monitoring**: Real-time VPN status notifications with location data and DNS leak detection
- **🌐 External Access**: All services accessible via mapped ports
- **🔗 Internal Communication**: Containers communicate via localhost
- **🛡️ Security**: Rate limiting, API authentication, isolated monitoring
- **🏠 Synology Compatible**: Tested and working on Synology NAS systems
- **🎯 Media Agnostic**: No media server included - works with any media player solution

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Host Network                              │
│  ┌─────────────────────────────────────────────────────────┐│
│  │                 Gluetun VPN Gateway                     ││
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐      ││
│  │  │qBittorr │ │ Sonarr  │ │ Radarr  │ │ Jackett │ ...  ││
│  │  │   ent   │ │         │ │         │ │         │      ││
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘      ││
│  │                                                         ││
│  │  ┌─────────────────────────────────────────────────┐   ││
│  │  │              Keepalive Client                   │   ││
│  │  │            (VPN Status Monitor)                 │   ││
│  │  └─────────────────────────────────────────────────┘   ││
│  └─────────────────────────────────────────────────────────┘│
│                           │                                 │
│                           │ Internet (via VPN)             │
│                           │                                 │
│                           │  ┌─────────────────────────┐   │
│                           │  │     Keepalive Server    │   │
│                           └─►│  (Real IP + Telegram)   │   │
│                              │     Network Monitor     │   │
│                              └─────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Why Gluetun?

This project uses [Gluetun](https://github.com/qdm12/gluetun) as the VPN gateway because it provides exceptional out-of-the-box support for an extensive list of VPN providers:

**Supported Providers:**
- **Commercial VPNs**: AirVPN, Cyberghost, ExpressVPN, FastestVPN, Giganews, HideMyAss, IPVanish, IVPN, Mullvad, NordVPN, Perfect Privacy, Privado, Private Internet Access, PrivateVPN, ProtonVPN, PureVPN, SlickVPN, Surfshark, TorGuard, VPNSecure.me, VPNUnlimited, Vyprvpn, WeVPN, Windscribe

**Protocol Support:**
- **OpenVPN**: Full support for all providers listed above
- **Wireguard**: Both kernelspace and userspace implementations
  - Native support: AirVPN, FastestVPN, IVPN, Mullvad, NordVPN, Perfect Privacy, ProtonVPN, Surfshark, Windscribe
  - Custom provider support: Cyberghost, Private Internet Access, PrivateVPN, PureVPN, Torguard, VPN Unlimited, VyprVPN, WeVPN
  - Custom Wireguard configurations

See the [Gluetun GitHub repository](https://github.com/qdm12/gluetun) for complete specifications and the full provider list.

## 📦 Stack Components

This stack includes the following services from the [**Servarr**](https://wiki.servarr.com/) ecosystem:

### 🎬 **Media Management (*arr Applications)**
The *arr applications are part of the Servarr project - a collection of applications for automated media management:

- **[Sonarr](https://sonarr.tv/)** - TV Show automation and management
- **[Radarr](https://radarr.video/)** - Movie automation and management  
- **[Readarr](https://readarr.com/)** - Book and audiobook automation
- **[Bazarr](https://bazarr.media/)** - Subtitle automation for Sonarr and Radarr
- **[Prowlarr](https://prowlarr.com/)** - Indexer management for all *arr apps

### 🔍 **Indexing & Search**
- **[Jackett](https://github.com/Jackett/Jackett)** - Torrent indexer proxy server
- **[FlareSolverr](https://github.com/FlareSolverr/FlareSolverr)** - Cloudflare bypass for protected sites

### ⬬ **Download Client**  
- **[qBittorrent](https://qbittorrent.org/)** - Feature-rich BitTorrent client with web interface

### 🛡️ **VPN & Monitoring**
- **[Gluetun](https://github.com/qdm12/gluetun)** - Lightweight VPN client in a thin Docker container
- **Custom Keepalive System** - VPN monitoring with Telegram notifications

### 📖 **What is Servarr?**
[**Servarr**](https://wiki.servarr.com/) is a collection of applications designed to automatically grab, sort, organize, and monitor your media collections. The *arr suite provides a unified ecosystem for managing different types of media content with consistent APIs and interfaces.

## 🚀 Quick Start

### 1. Prerequisites

- Docker and Docker Compose installed
- VPN provider account (PrivateVPN supported out of the box)
- Telegram bot token (optional, for monitoring)
- **Synology NAS**: Fully compatible and tested on Synology systems

### 2. Telegram Bot Setup (Optional but Recommended)

For VPN monitoring notifications, you'll need a Telegram bot:

1. **Create a Telegram Bot:**
   - Message [@BotFather](https://t.me/botfather) on Telegram
   - Send `/newbot` and follow the prompts
   - Choose a name and username for your bot
   - Copy the **Bot Token** (format: `1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ`)

2. **Get Your Chat ID:**
   - Start a chat with your new bot
   - Message [@userinfobot](https://t.me/userinfobot) to get your Chat ID
   - Or visit `https://api.telegram.org/bot<YourBotToken>/getUpdates` after messaging your bot

3. **Test the Setup:**
   ```bash
   # Replace with your actual values
   curl -X POST "https://api.telegram.org/bot<BOT_TOKEN>/sendMessage" \
        -d "chat_id=<CHAT_ID>&text=Test message from VPN Media Stack"
   ```

### 3. Setup

```bash
# Clone or download this repository
git clone <repository-url>
cd vpn-media-stack

# Copy and configure environment file
cp .env.example .env
nano .env  # Fill in your configuration (including Telegram values)

# The compose.yaml file is ready to use - no copying needed!

# Create necessary directories
mkdir -p gluetun qbittorrent/config jackett/{config,downloads} 
mkdir -p prowlarr/config sonarr/config radarr/config readarr/config
mkdir -p bazarr/config radarr_ES/config

# Start the stack
docker compose up -d
```

### 4. First-time Configuration

After starting the containers, configure each service:

1. **qBittorrent** (`http://your-server:9802`)
   - Default login: `admin` / `adminadmin` (change immediately)
   - Configure download directories: `/downloads` and `/incomplete`

2. **Prowlarr** (`http://your-server:9805`) 
   - Add indexers and connect to other *arr apps
   - Configure FlareSolverr: `http://localhost:8191`

3. **Sonarr** (`http://your-server:9804`)
   - Add Prowlarr as indexer
   - Configure qBittorrent: `http://localhost:8085`

4. **Radarr** (`http://your-server:9809`)
   - Similar configuration to Sonarr

5. **Radarr ES** (`http://your-server:9811`)
   - Spanish language movie instance (added for testing purposes)
   - Configure separately from main Radarr instance

6. **Other Services**: Configure as needed

### 🎭 **Media Player Integration**

This stack is **intentionally media player agnostic** - no media server (Plex, Jellyfin, Emby) is included to keep the setup completely isolated and flexible. You can connect it to any media server solution:

**Popular Options:**
- **[Emby](https://emby.media/)** - Feature-rich media server (recommended for integration)  
- **[Plex](https://www.plex.tv/)** - Popular media server with premium features
- **[Jellyfin](https://jellyfin.org/)** - Free and open-source media server

The downloaded media will be organized in your configured media directories, ready for any media server to scan and serve.

## 🔧 Configuration

### Environment Variables

Key variables to configure in `.env`:

```bash
# VPN Configuration
VPN_SERVICE_PROVIDER=privatevpn
VPN_USER=your_vpn_username
VPN_PASSWORD=your_vpn_password
SERVER_COUNTRIES=Switzerland,Netherlands

# Paths (adjust to your setup)
VOLUME_DOCKER_PROJECT=/path/to/docker/project
VOLUME_DOWNLOADS=/path/to/downloads
VOLUME_MEDIA=/path/to/media

# User IDs (get with 'id username')
PUID_MEDIA=1000
PGID_MEDIA=1000

# Telegram Monitoring (optional)
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id
```

### Port Mapping

| Service     | External Port | Internal Port | Access URL                    |
|-------------|---------------|---------------|-------------------------------|
| qBittorrent | 9802          | 8085          | http://your-server:9802       |
| Jackett     | 9803          | 9117          | http://your-server:9803       |
| Sonarr      | 9804          | 8989          | http://your-server:9804       |
| Prowlarr    | 9805          | 9696          | http://your-server:9805       |
| FlareSolverr| 9806          | 8191          | http://your-server:9806       |
| Readarr     | 9808          | 8787          | http://your-server:9808       |
| Radarr      | 9809          | 7878          | http://your-server:9809       |
| Bazarr      | 9810          | 6767          | http://your-server:9810       |
| Radarr ES   | 9811          | 7879          | http://your-server:9811       |
| Keepalive   | 5421          | 5000          | http://your-server:5421       |

### Accessing Services from Your Network

To access the web interfaces from your local network (e.g., from your computer's browser), use the **external ports** mapped to your Docker host:

**Examples:**
- **Radarr**: `http://192.168.1.100:9809/` (replace with your server's IP)
- **Sonarr**: `http://server.local:9804/`
- **qBittorrent**: `http://your-server:9802/`
- **Prowlarr**: `http://10.0.0.50:9805/`

**Note**: Replace `your-server` with your actual server IP address or hostname. The containers are accessible from any device on your network via these external ports.

## 🔒 Security Features

### VPN Protection
- All media containers route traffic through Gluetun VPN
- External IP verification via multiple sources
- Automatic VPN reconnection on failures

### Monitoring System
- **Keepalive Client**: Monitors VPN connectivity from inside the VPN tunnel
- **Keepalive Server**: Isolated monitoring server with real IP
- **Telegram Integration**: Real-time alerts for VPN disconnections with location and DNS leak information
- **Network Status Notifications**: Automatic alerts when VPN connection is lost or restored
- **Rate Limiting**: 30 requests per minute API protection
- **API Authentication**: Secure API key-based access

### Network Isolation
- Media containers: Shared VPN network (10.50.0.0/16)
- Monitoring: Isolated network (10.51.0.0/16)
- Firewall rules prevent unauthorized access

### Telegram Notifications Examples

The monitoring system sends comprehensive alerts via Telegram:

**🔴 VPN Disconnection Alert:**
```
🚨 VPN CONNECTION LOST! 🚨
Client: synology-vpn-media
Time: 2025-10-12 15:30:15 (Europe/Madrid)
External IP: 79.117.62.134 (Real IP detected!)
Location: Spain, Madrid
ISP: Your Internet Provider
DNS Servers: 8.8.8.8, 1.1.1.1 (DNS leak detected!)
⚠️ Media services are now exposed!
```

**✅ VPN Restored Alert:**
```
✅ VPN CONNECTION RESTORED ✅
Client: synology-vpn-media  
Time: 2025-10-12 15:35:22 (Europe/Madrid)
External IP: 185.72.199.129 (VPN IP)
Location: Poland, Kujawsko-Pomorskie, Toruń
ISP: PrivateVPN
DNS Servers: VPN DNS (secure)
🛡️ All traffic now protected via VPN
```

**📊 Status Check Response:**
```
📊 VPN STATUS REPORT 📊
Client: synology-vpn-media
Status: ✅ CONNECTED
External IP: 185.72.199.129
Location: Poland, Toruń
Connected since: 2 hours 15 minutes
Last check: 30 seconds ago
```

## 🛠️ Advanced Usage

### Container Communication
Containers sharing the VPN network communicate via `localhost`:

```bash
# From qBittorrent to Jackett
curl http://localhost:9117

# From Sonarr to Prowlarr  
curl http://localhost:9696
```

### Monitoring Commands
```bash
# Check VPN status and external IP
docker exec gluetun wget -qO- https://ipinfo.io/ip

# DNS leak test (should show VPN DNS servers)
docker exec gluetun nslookup google.com
docker exec gluetun wget -qO- https://www.dnsleaktest.com/

# Verify container connectivity
docker exec qbittorrent curl -s http://localhost:9117

# View keepalive logs
docker logs keepalive-server
docker logs keepalive-client
```

### Troubleshooting
```bash
# Check container status
docker compose ps

# View logs
docker compose logs gluetun
docker compose logs qbittorrent

# Restart VPN container
docker compose restart gluetun

# Check network connectivity
docker exec gluetun ping 8.8.8.8
docker exec qbittorrent curl -s https://ipinfo.io/json
```

## 📁 Directory Structure

```
vpn-media-stack/
├── compose.yaml                 # Main Docker Compose configuration
├── .env.example                 # Environment variables template  
├── keepalive-server/
│   └── keepalive-server.py     # Monitoring server script
├── keepalive-client/
│   └── keepalive.sh           # Monitoring client script
└── README.md                  # This file
```

Runtime directories (created automatically):
```
VOLUME_DOCKER_PROJECT/
├── gluetun/                    # VPN configuration and logs
├── qbittorrent/config/         # qBittorrent settings
├── jackett/config/             # Jackett configuration
├── prowlarr/config/            # Prowlarr settings
├── sonarr/config/              # Sonarr configuration
├── radarr/config/              # Radarr settings
├── readarr/config/             # Readarr configuration
├── bazarr/config/              # Bazarr settings
└── radarr_ES/config/           # Spanish Radarr instance
```

## 🔄 Updates

```bash
# Update all containers
docker compose pull
docker compose up -d

# Update specific container
docker compose pull gluetun
docker compose up -d gluetun
```

## 🐛 Common Issues

### VPN Not Connecting
1. Check VPN credentials in `.env`
2. Verify server countries are available
3. Check Gluetun logs: `docker logs gluetun`

### Services Not Accessible
1. Ensure containers are running: `docker compose ps`
2. Check port conflicts: `netstat -tlnp | grep 980`
3. Verify firewall settings

### Container Communication Issues
1. Containers using `network_mode: service:gluetun` communicate via `localhost`
2. Use internal ports, not external mapped ports
3. Check network configuration: `docker network ls`

### Synology-Specific Issues
Synology NAS systems can be challenging with Docker networking configurations:

1. **Docker Package**: Ensure Docker package is installed from Package Center
2. **User Permissions**: Run containers with proper PUID/PGID for your Synology user
3. **Volume Paths**: Use `/volume1/docker/...` paths as shown in examples
4. **Port Conflicts**: Check for conflicts with Synology services (avoid ports 5000, 5001, etc.)
5. **Network Mode**: Synology supports `network_mode: service:` configurations
6. **File Permissions**: Use the provided chown commands for proper permissions

**Synology Compatibility**: This stack has been specifically tested and verified to work on Synology NAS systems, addressing common networking and permission challenges.

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 👨‍💻 Project Background

**🚨 IMPORTANT: This is an EDUCATIONAL PROJECT created for technical demonstration purposes only.**

This project was created as a practical demonstration of Docker containerization and networking concepts. Originally developed for a friend who requested a comprehensive VPN-protected media stack, it serves as an example of:

- **Docker Compose orchestration** with complex networking requirements
- **VPN integration** and traffic routing through containerized gateways  
- **Service isolation** and inter-container communication patterns
- **Security implementation** with monitoring and alerting systems
- **Real-world application** of Docker networking concepts

### 🔴 Personal Disclaimer

**I (the author) am NOT using this setup for any media downloading or torrenting activities.** This project was developed purely as:

1. **Technical Exercise**: To demonstrate Docker networking and containerization skills
2. **Educational Resource**: To help others learn about VPN integration and service orchestration
3. **Portfolio Project**: To showcase practical DevOps and system administration knowledge
4. **Learning Tool**: For understanding complex Docker networking scenarios

The implementation focuses on the **technical architecture** rather than content acquisition.

## ⚠️ Legal Disclaimer

**🚨 CRITICAL**: This setup is provided **EXCLUSIVELY** for educational and technical demonstration purposes only.

**THE AUTHOR DOES NOT USE THIS SYSTEM** and created it solely for educational and technical demonstration purposes.

### 🚨 Legal Responsibilities
- **Review Local Laws**: Torrenting, VPN usage, and media downloading laws vary significantly by country and jurisdiction
- **Content Compliance**: Ensure all downloaded content complies with local copyright laws
- **VPN Regulations**: Some countries restrict or prohibit VPN usage
- **ISP Terms**: Review your internet service provider's terms of service
- **Personal Responsibility**: Users are solely responsible for their actions and compliance with applicable laws

### 📖 Educational Use
- This project demonstrates technical concepts in containerization and networking
- Knowledge gained should be applied responsibly and legally
- Consider supporting content creators and legal distribution platforms

### 🛡️ Liability
The authors and contributors are not responsible for:
- Any misuse of this software or configuration
- Legal consequences resulting from improper use
- Violation of local laws or terms of service
- Any damages or issues arising from use of this setup

**Use at your own risk and ensure full compliance with all applicable laws and regulations.**
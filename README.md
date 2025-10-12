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

### 🤖 Telegram Bot Commands

The keepalive server includes an interactive Telegram bot with the following commands:

| Command | Description | Response |
|---------|-------------|----------|
| `/help` | Show available commands | Complete command list with descriptions |
| `/status` | Get current VPN status | Detailed status report with IP, location, and uptime |
| `/ping` | Test bot connectivity | Simple "Pong!" response to verify bot is working |
| **Other text** | Any other text or commands | Friendly response with guidance to use /help |

**Example Bot Interactions:**
```
User: /help
Bot: 🤖 VPN Media Stack Bot Commands:
     /status - Get current VPN status
     /ping - Test bot connectivity  
     /help - Show this help message

User: /status
Bot: 📊 VPN STATUS REPORT 📊
     Client: synology-vpn-media
     Status: ✅ CONNECTED
     External IP: 185.170.104.53
     Location: Poland, Toruń
     Last check: 30 seconds ago

User: /ping  
Bot: 🏓 Pong! Bot is online and monitoring your VPN.

User: hello
Bot: 👋 Hello! I'm your VPN monitoring bot.
     I received: hello
     Use /help to see available commands.
     
     Available commands:
     🏓 /ping - Test connectivity
     📊 /status - Get VPN status  
     ❓ /help - Show help
```

**Automatic Monitoring Alerts:**
- **No Clients Alert**: Sent when no keepalive clients are reporting (every 5 minutes)
- **VPN Disconnection**: Immediate alert when VPN connection is lost
- **VPN Restoration**: Confirmation when VPN connection is restored
- **DNS Leak Detection**: Automatic warnings when DNS leaks are detected

## 🛠️ Advanced Usage

### Container Communication
Containers sharing the VPN network communicate via `localhost`:

```bash
# From qBittorrent to Jackett
curl http://localhost:9117

# From Sonarr to Prowlarr  
curl http://localhost:9696
```

### 📊 VPN Status Monitoring

The stack includes comprehensive VPN monitoring through Gluetun logs and the custom keepalive system:

#### **Successful VPN Connection Logs**
When everything is working correctly, you'll see logs similar to this:

```log
gluetun  | 2025-10-12T10:39:28Z INFO [firewall] allowing VPN connection...
gluetun  | 2025-10-12T10:39:29Z INFO [openvpn] OpenVPN 2.6.11 x86_64-alpine-linux-musl
gluetun  | 2025-10-12T10:39:29Z INFO [openvpn] [PrivateVPN] Peer Connection Initiated with [AF_INET]91.236.55.255:1194
gluetun  | 2025-10-12T10:39:30Z INFO [openvpn] Initialization Sequence Completed
gluetun  | 2025-10-12T10:39:31Z INFO [healthcheck] healthy!
gluetun  | 2025-10-12T10:39:50Z INFO [ip getter] Public IP address is 185.170.104.53 (Poland, Kujawsko-Pomorskie, Toruń - source: ipinfo)
gluetun  | 2025-10-12T10:40:16Z INFO [dns] DNS server listening on [::]:53
gluetun  | 2025-10-12T10:40:16Z INFO [dns] ready
```

**Key Indicators of Successful VPN Connection:**
- ✅ **Firewall**: `allowing VPN connection...`
- ✅ **OpenVPN**: `Initialization Sequence Completed`
- ✅ **Health Check**: `healthy!` (appears regularly)
- ✅ **Public IP**: Shows VPN server location (e.g., `185.170.104.53 (Poland, Toruń)`)
- ✅ **DNS Server**: `DNS server listening` and `ready`

#### **VPN Monitoring Commands**
```bash
# Check current VPN status and external IP
docker exec gluetun wget -qO- https://ipinfo.io/ip
# Expected: VPN server IP (e.g., 185.170.104.53)

# Get detailed location information
docker exec gluetun wget -qO- https://ipinfo.io/json
# Shows: IP, city, region, country, org, timezone

# DNS leak test (should show VPN DNS servers)
docker exec gluetun nslookup google.com
docker exec gluetun wget -qO- https://1.1.1.1/cdn-cgi/trace

# Verify container connectivity through VPN
docker exec qbittorrent curl -s http://localhost:9117
docker exec sonarr curl -s https://ipinfo.io/ip

# View real-time VPN logs
docker logs gluetun --follow

# Check VPN health status
docker exec gluetun wget -qO- http://localhost:9999/health
```

#### **DNS Leak Detection Testing**
Your stack includes automated DNS leak detection. Test it manually:

```bash
# Method 1: Compare VPN IP location with DNS resolver location
VPN_COUNTRY=$(docker exec gluetun wget -qO- https://ipinfo.io/json | grep country | cut -d'"' -f4)
DNS_COUNTRY=$(docker exec gluetun wget -qO- https://1.1.1.1/cdn-cgi/trace | grep '^loc=' | cut -d'=' -f2)
echo "VPN Country: $VPN_COUNTRY, DNS Country: $DNS_COUNTRY"
# Should match if no leak (e.g., both show "PL" for Poland)

# Method 2: Check DNS servers being used
docker exec gluetun cat /etc/resolv.conf
# Should show: nameserver 127.0.0.1 (Gluetun's DNS server)

# Method 3: Test external DNS leak detection services
docker exec gluetun wget -qO- "https://bash.ws/dnsleak"
```

#### **Keepalive System Status**
Monitor the custom keepalive system that provides Telegram notifications:

```bash
# View keepalive client logs (runs inside VPN)
docker logs keepalive-client --tail 20

# Expected successful output:
# ✅ Keepalive sent successfully at [timestamp]
#    📍 Location: Toruń, Kujawsko-Pomorskie, PL
#    🌐 VPN IP: 185.170.104.53
#    🔒 DNS: PL (WAW) - No leak detected

```bash
# View keepalive server logs (isolated network)
docker logs keepalive-server --tail 20

# Check server status endpoint
curl -s http://localhost:5421/status | jq

# Monitor real-time activity
docker compose logs -f keepalive-client keepalive-server
```

#### **📋 Log Pattern Recognition**

**Keepalive Client Success Patterns:**
```log
✅ Keepalive sent successfully at [timestamp]
📍 Location: Toruń, Kujawsko-Pomorskie, PL
🌐 VPN IP: 185.170.104.53
🔒 DNS: PL (WAW) - No leak detected
⏳ Waiting 300 seconds until next keepalive...
```

**Keepalive Server Activity Patterns:**
```log
🌐 API Access: keepalive | IP: 10.51.0.1 | Auth: WITH_KEY | Status: 200_OK
Keepalive received from synology-vpn-media - IP: 185.170.104.53
✅ Telegram message sent successfully
📥 Telegram command received: /status
🔍 Monitoring check: 1 client(s) registered
```

**Telegram Bot Interaction Logs:**
```log
[timestamp] 📥 Telegram command received: /help
[timestamp] ✅ Telegram message sent successfully  
[timestamp] ✅ Help response sent
[timestamp] 📥 Telegram command received: /ping
[timestamp] ✅ Pong response sent
[timestamp] 📥 Telegram command received: /status
[timestamp] ✅ Status response sent
```
```

### **🚨 Warning Signs to Watch For**

❌ **VPN Connection Issues:**
```log
gluetun  | ERROR [openvpn] authentication failed
gluetun  | ERROR [firewall] cannot allow VPN connection
gluetun  | WARN [healthcheck] unhealthy!
```

❌ **DNS Leak Indicators:**
```bash
# If this shows your real IP instead of VPN IP:
docker exec qbittorrent curl -s https://ipinfo.io/ip

# If countries don't match:
VPN: "ES" (Spain), DNS: "US" (United States) = LEAK DETECTED!
```

❌ **Network Isolation Failures:**
```log
keepalive-client | ❌ Failed to send keepalive
keepalive-client | 📍 Location: Unknown, Unknown, Unknown
```

### Troubleshooting

#### 🔍 **Real-World Log Analysis**

Based on actual deployment scenarios, here are common issues and their solutions:

**📋 Checking System Status:**
```bash
# Check all container status
docker compose ps

# View real-time logs for specific services
docker compose logs -f gluetun
docker compose logs -f keepalive-client
docker compose logs -f keepalive-server

# Check recent logs with timestamps
docker compose logs -t --tail=20 keepalive-client
```

#### 🚨 **Common Issue: DNS Leak Testing Mode Still Active**

**Problem:** Keepalive client shows testing mode even after script updates:
```log
keepalive-client  | ⚠️  TESTING MODE: Simulating DNS leak - DNS location forced to US
keepalive-client  | ❌ Failed to send keepalive to http://duevite.eu:5421
```

**Root Cause:** Container is running older version of the script despite file updates.

**Solution:** Restart the keepalive-client container to pick up script changes:
```bash
# Restart specific container to reload scripts
docker compose restart keepalive-client

# Verify the fix - should show normal operation:
docker compose logs -f keepalive-client
```

**Expected Output After Fix:**
```log
keepalive-client  | ✅ Keepalive sent successfully at [timestamp]
keepalive-client  |    📍 Location: Toruń, Kujawsko-Pomorskie, PL
keepalive-client  |    🌐 VPN IP: 185.170.104.53
keepalive-client  |    🔒 DNS: PL (WAW) - No leak detected
```

#### 🌐 **Network Connectivity Issues**

**Problem:** Keepalive client cannot reach server:
```log
keepalive-client  | ❌ Failed to send keepalive to http://duevite.eu:5421
keepalive-client  | 📍 Location: Unknown, Unknown, Unknown
```

**Diagnostic Steps:**
```bash
# Test VPN connectivity from inside VPN network
docker exec keepalive-client curl -s https://ipinfo.io/json

# Test keepalive server connectivity
docker exec keepalive-client curl -v http://duevite.eu:5421/status

# Check if server is accessible from host
curl -s http://localhost:5421/status
```

#### 📊 **Telegram Bot Verification**

**Check Bot Functionality:**
```bash
# View server logs for Telegram activity
docker compose logs keepalive-server | grep -i telegram

# Expected successful patterns:
# [timestamp] ✅ Telegram message sent successfully
# [timestamp] 📥 Telegram command received: /status
# [timestamp] ✅ Status response sent
```

**Test Bot Commands:**
- Send `/ping` → Should receive "🏓 Pong!"
- Send `/status` → Should receive current VPN status
- Send `/help` → Should receive command list
- Send any other text → Should receive friendly response with guidance to use /help

#### 🔄 **Container Restart Sequence**

**When containers fail to communicate:**
```bash
# Restart in proper order
docker compose restart gluetun          # VPN gateway first
sleep 10
docker compose restart keepalive-client # Then monitoring
docker compose restart keepalive-server # Server can restart anytime

# Verify everything is working
docker compose ps
docker compose logs --tail=10 keepalive-client
```

#### 📈 **Success Indicators**

**Healthy System Logs:**
```log
# Gluetun VPN:
gluetun  | INFO [openvpn] Initialization Sequence Completed
gluetun  | INFO [healthcheck] healthy!
gluetun  | INFO [ip getter] Public IP address is 185.170.104.53 (Poland...)

# Keepalive Client:
keepalive-client  | ✅ Keepalive sent successfully
keepalive-client  | 🔒 DNS: PL (WAW) - No leak detected

# Keepalive Server:  
keepalive-server  | Keepalive received from synology-vpn-media
keepalive-server  | ✅ Telegram message sent successfully
```

#### 🛠️ **General Troubleshooting Commands**

```bash
# Check container status
docker compose ps

# View logs for specific issues
docker compose logs gluetun | grep -i error
docker compose logs qbittorrent | grep -i fail

# Test network connectivity
docker exec gluetun ping 8.8.8.8
docker exec qbittorrent curl -s https://ipinfo.io/json

# Restart VPN container if connection issues
docker compose restart gluetun

# Full system restart (nuclear option)
docker compose down && docker compose up -d
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
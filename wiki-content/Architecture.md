# Architecture & Technical Deep-dive

Comprehensive technical documentation of SafeHarbor's architecture, networking, and implementation details.

## 🏗️ System Architecture

### Overview Diagram

```
┌───────────────────────────────────────────────────────────────────────────────┐
│                               HOST SYSTEM                                     │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────────┐  │
│  │                          VPN NETWORK (10.50.0.0/16)                    │  │
│  │                                                                         │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐   │  │
│  │  │                    GLUETUN VPN GATEWAY                          │   │  │
│  │  │  • OpenVPN/Wireguard client                                    │   │  │
│  │  │  • Firewall and routing                                        │   │  │
│  │  │  • DNS server (127.0.0.1:53)                                   │   │  │
│  │  │  • Health monitoring                                           │   │  │
│  │  └─────────────────────────────────────────────────────────────────┘   │  │
│  │                                │                                        │  │
│  │  ┌─────────────────────────────┴───────────────────────────────────┐   │  │
│  │  │                    MEDIA SERVICES                               │   │  │
│  │  │                                                                 │   │  │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │   │  │
│  │  │  │qBittorrent  │ │   Sonarr    │ │   Radarr    │              │   │  │
│  │  │  │ Port: 8085  │ │ Port: 8989  │ │ Port: 7878  │              │   │  │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘              │   │  │
│  │  │                                                                 │   │  │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐              │   │  │
│  │  │  │  Prowlarr   │ │   Jackett   │ │   Bazarr    │              │   │  │
│  │  │  │ Port: 9696  │ │ Port: 9117  │ │ Port: 6767  │              │   │  │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘              │   │  │
│  │  └─────────────────────────────────────────────────────────────────┘   │  │
│  │                                │                                        │  │
│  │  ┌─────────────────────────────┴───────────────────────────────────┐   │  │
│  │  │              KEEPALIVE CLIENT (VPN MONITOR)                     │   │  │
│  │  │  • Monitors VPN status from inside tunnel                      │   │  │
│  │  │  • DNS leak detection                                          │   │  │
│  │  │  • Geolocation tracking                                        │   │  │
│  │  │  • Reports to external server                                  │   │  │
│  │  └─────────────────────────────────────────────────────────────────┘   │  │
│  └─────────────────────────────────────────────────────────────────────────┘  │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────────┐  │
│  │                    MONITORING NETWORK (10.51.0.0/16)                   │  │
│  │                                                                         │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐   │  │
│  │  │                 KEEPALIVE SERVER                                │   │  │
│  │  │  • Flask web server (port 5000)                               │   │  │
│  │  │  • Receives VPN status reports                                 │   │  │
│  │  │  • Telegram bot integration                                    │   │  │
│  │  │  • Alert system & notifications                               │   │  │
│  │  │  • REST API endpoints                                          │   │  │
│  │  │  • Uses host's real IP (not VPN)                              │   │  │
│  │  └─────────────────────────────────────────────────────────────────┘   │  │
│  └─────────────────────────────────────────────────────────────────────────┘  │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────────┐  │
│  │                        HOST NETWORK                                     │  │
│  │  Port Mappings (Host → Container):                                      │  │
│  │  • 9802 → qBittorrent:8085   • 9803 → Jackett:9117                    │  │
│  │  • 9804 → Sonarr:8989        • 9805 → Prowlarr:9696                   │  │
│  │  • 9808 → Readarr:8787       • 9809 → Radarr:7878                     │  │
│  │  • 9810 → Bazarr:6767        • 9811 → Radarr-ES:7879                  │  │
│  │  • 5421 → Keepalive:5000                                               │  │
│  └─────────────────────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                              EXTERNAL INTERNET                                │
│                                                                               │
│  ┌─────────────────────────┐    ┌─────────────────────────────────────────┐  │
│  │      VPN PROVIDER       │    │           TELEGRAM API                  │  │
│  │   (PrivateVPN, etc.)    │    │        (Bot notifications)              │  │
│  │                         │    │                                         │  │
│  │  • OpenVPN servers      │    │  • Real-time messaging                 │  │
│  │  • Wireguard endpoints  │    │  • Command processing                  │  │
│  │  • Multiple countries   │    │  • HTTPS encrypted                     │  │
│  └─────────────────────────┘    └─────────────────────────────────────────┘  │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────────┐  │
│  │                    GEOLOCATION SERVICES                                 │  │
│  │  • ipinfo.io - IP geolocation and ISP data                            │  │
│  │  • 1.1.1.1/cdn-cgi/trace - Cloudflare DNS location                    │  │
│  │  • httpbin.org - IP testing and validation                            │  │
│  └─────────────────────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────────────────────┘
```

## 🌐 Network Architecture

### Network Segmentation

SafeHarbor uses three isolated network segments:

#### 1. VPN Network (10.50.0.0/16)
**Purpose:** Secure media services through VPN tunnel

**Components:**
- Gluetun VPN gateway
- All *arr applications (Sonarr, Radarr, etc.)
- qBittorrent download client
- Keepalive client (VPN monitor)

**Characteristics:**
- All traffic routed through VPN
- Internal communication via `localhost`
- DNS provided by Gluetun (127.0.0.1)
- No direct internet access (VPN-only)

#### 2. Monitoring Network (10.51.0.0/16)
**Purpose:** Isolated monitoring with real IP access

**Components:**
- Keepalive server
- Telegram bot functionality

**Characteristics:**
- Uses host's real IP (not VPN)
- Can communicate with external APIs
- Isolated from VPN network
- Receives status via HTTP API

#### 3. Host Network
**Purpose:** External access and port mapping

**Components:**
- Port mappings for web interfaces
- Direct host system access

### Network Communication Flow

```
┌─────────────────┐    localhost     ┌─────────────────┐
│    Sonarr       │ ◄──────────────► │   Prowlarr      │
│   (port 8989)   │                  │  (port 9696)    │
└─────────────────┘                  └─────────────────┘
         │                                     │
         │ localhost                           │ localhost  
         ▼                                     ▼
┌─────────────────┐    localhost     ┌─────────────────┐
│  qBittorrent    │ ◄──────────────► │    Jackett      │
│   (port 8085)   │                  │  (port 9117)    │
└─────────────────┘                  └─────────────────┘
         │
         │ ALL TRAFFIC ROUTED THROUGH VPN
         ▼
┌─────────────────────────────────────────────────────────┐
│                GLUETUN VPN GATEWAY                      │
│  ┌─────────────────┐              ┌─────────────────┐  │
│  │   Firewall      │              │  DNS Server     │  │
│  │   Rules         │              │  (127.0.0.1)    │  │
│  └─────────────────┘              └─────────────────┘  │
│                          │                             │
│  ┌─────────────────────────────────────────────────┐   │
│  │            VPN TUNNEL                           │   │
│  │      (OpenVPN/Wireguard)                        │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
                 INTERNET (VPN Provider)
```

## 🔧 Container Architecture

### Container Relationships

```yaml
# Docker Compose service dependencies and relationships

gluetun:                    # VPN Gateway (base service)
  ├── provides: VPN tunnel, DNS, networking
  └── exposes: health check endpoint (port 9999)

qbittorrent:               # Download Client  
  ├── depends_on: gluetun
  ├── network_mode: service:gluetun
  └── communicates_via: localhost:8085

sonarr:                    # TV Shows
  ├── depends_on: gluetun  
  ├── network_mode: service:gluetun
  ├── connects_to: prowlarr (localhost:9696)
  └── connects_to: qbittorrent (localhost:8085)

radarr:                    # Movies
  ├── depends_on: gluetun
  ├── network_mode: service:gluetun  
  ├── connects_to: prowlarr (localhost:9696)
  └── connects_to: qbittorrent (localhost:8085)

prowlarr:                  # Indexer Manager
  ├── depends_on: gluetun
  ├── network_mode: service:gluetun
  ├── manages: sonarr, radarr indexers
  └── connects_to: jackett (localhost:9117)

keepalive-client:          # VPN Monitor (inside VPN)
  ├── depends_on: gluetun
  ├── network_mode: service:gluetun
  ├── monitors: VPN status, DNS leaks
  └── reports_to: keepalive-server (external API)

keepalive-server:          # Monitor Server (real IP)
  ├── network: monitoring-network
  ├── receives: client status reports
  ├── provides: REST API, Telegram bot
  └── alerts: via Telegram notifications
```

### Service Communication Patterns

#### Internal Communication (VPN Network)
```bash
# All services communicate via localhost within VPN network
sonarr → prowlarr:        http://localhost:9696/api/v1/
radarr → prowlarr:        http://localhost:9696/api/v1/
sonarr → qbittorrent:     http://localhost:8085/api/v2/
prowlarr → jackett:       http://localhost:9117/api/v2.0/
```

#### External Communication (Monitoring)
```bash
# Keepalive client → server (crosses network boundary)
keepalive-client → keepalive-server: http://duevite.eu:5421/keepalive

# Server → Telegram API (real IP)
keepalive-server → telegram: https://api.telegram.org/bot{token}/
```

## 🛡️ Security Architecture

### VPN Security Model

#### Traffic Flow Security
```
User Request → Host Port → Container → Gluetun → VPN Tunnel → Internet
     ↑              ↑           ↑          ↑           ↑
  External      Port Map    localhost   VPN GW    Encrypted
   Access                   Internal               Tunnel
```

#### DNS Security
```
Container DNS Query → Gluetun DNS (127.0.0.1) → VPN Provider DNS → Response
                           ↑                         ↑
                    Prevents leaks            Protected DNS
```

### Network Isolation

#### VPN Network Isolation
- **Ingress:** Only from Gluetun VPN gateway
- **Egress:** Only through VPN tunnel  
- **Internal:** Container-to-container via localhost
- **DNS:** Gluetun-provided DNS only (no ISP DNS)

#### Monitoring Network Isolation  
- **Ingress:** HTTP API on port 5000
- **Egress:** Real IP for Telegram/external APIs
- **Security:** API key authentication, rate limiting
- **Isolation:** No access to VPN network containers

### API Security

#### Authentication
```python
# API key validation for keepalive endpoints
@app.route('/keepalive', methods=['POST'])
def receive_keepalive():
    api_key = request.headers.get('X-API-Key')
    if API_KEY and api_key != API_KEY:
        return jsonify({"error": "Invalid API key"}), 401
```

#### Rate Limiting  
```python
# Per-IP rate limiting (30 requests/minute default)
@limiter.limit("30 per minute")  
def receive_keepalive():
    # Process request
```

#### IP Whitelisting (Optional)
```python
# Restrict access to specific IPs
ALLOWED_IPS = ['10.51.0.1', '192.168.1.100']
if ALLOWED_IPS and request.remote_addr not in ALLOWED_IPS:
    return jsonify({"error": "IP not allowed"}), 403
```

## 📊 Data Flow Architecture

### VPN Monitoring Data Flow

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        KEEPALIVE CLIENT                                 │
│                      (Inside VPN Network)                               │
│                                                                         │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    │
│  │  IP Detection   │    │ DNS Resolution  │    │  Geolocation    │    │
│  │                 │    │                 │    │                 │    │
│  │ ipinfo.io       │    │ 1.1.1.1/trace  │    │ Location data   │    │
│  │ httpbin.org     │    │ nslookup test   │    │ ISP information │    │
│  └─────────────────┘    └─────────────────┘    └─────────────────┘    │
│            │                       │                       │           │
│            └───────────────────────┼───────────────────────┘           │
│                                    ▼                                   │
│                          ┌─────────────────┐                          │
│                          │  Status Report  │                          │
│                          │   Generation    │                          │
│                          └─────────────────┘                          │
│                                    │                                   │
└────────────────────────────────────┼───────────────────────────────────┘
                                     │
                            HTTP POST /keepalive
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      KEEPALIVE SERVER                                   │
│                     (Real IP Network)                                   │
│                                                                         │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    │
│  │ Status Receiver │    │ Change Detector │    │ Alert Generator │    │
│  │                 │    │                 │    │                 │    │
│  │ HTTP endpoint   │    │ IP comparison   │    │ Telegram msgs   │    │
│  │ JSON parsing    │    │ DNS analysis    │    │ Notifications   │    │
│  │ Data validation │    │ Location diff   │    │ Status reports  │    │
│  └─────────────────┘    └─────────────────┘    └─────────────────┘    │
│           │                       │                       │           │
│           └───────────────────────┼───────────────────────┘           │
│                                   ▼                                   │
│                         ┌─────────────────┐                          │
│                         │   Data Storage  │                          │
│                         │  (In-memory)    │                          │
│                         └─────────────────┘                          │
│                                   │                                   │
└───────────────────────────────────┼───────────────────────────────────┘
                                    │
                                    ▼
                          ┌─────────────────┐
                          │  TELEGRAM API   │
                          │                 │
                          │ Bot commands    │
                          │ Notifications   │
                          │ Status updates  │
                          └─────────────────┘
```

### Configuration Data Flow

```
.env file → Docker Compose → Container Environment Variables → Application Config

Examples:
VPN_USER=john → GLUETUN_USER=john → OpenVPN auth
TELEGRAM_BOT_TOKEN=xxx → Bot initialization → Telegram API
PUID_MEDIA=1000 → Container user ID → File permissions
```

## 🔍 Monitoring Architecture

### Health Check System

#### Gluetun Health Monitoring
```yaml
# Built-in Gluetun health check
healthcheck:
  test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:9999"]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

#### Custom VPN Monitoring  
```bash
# Keepalive client monitoring (every 5 minutes)
while true; do
    # 1. Gather VPN status
    IP_INFO=$(curl -s https://ipinfo.io/json)
    DNS_INFO=$(curl -s https://1.1.1.1/cdn-cgi/trace)
    
    # 2. Detect changes and leaks
    detect_ip_changes()
    detect_dns_leaks()
    
    # 3. Report to server
    send_status_report()
    
    sleep 300  # 5 minutes
done
```

### Alert Processing Pipeline

```
Event Detection → Change Analysis → Alert Decision → Notification Delivery

Examples:
IP Change: 1.2.3.4 → 5.6.7.8 → Location diff → Alert → Telegram message
DNS Leak: VPN=PL, DNS=US → Leak detected → Alert → Immediate notification  
Offline: No report >5min → Timeout → Alert → Disconnection warning
```

## 🏗️ File System Architecture

### Volume Structure
```
VOLUME_DOCKER_PROJECT/
├── gluetun/                    # VPN configuration & logs
│   ├── gluetun.log
│   └── openvpn/
├── qbittorrent/config/         # Download client config
│   ├── qBittorrent.conf
│   └── categories/
├── sonarr/config/              # TV show management
│   ├── config.xml
│   ├── sonarr.db
│   └── logs/
├── radarr/config/              # Movie management  
│   ├── config.xml
│   ├── radarr.db
│   └── logs/
├── prowlarr/config/            # Indexer management
│   ├── config.xml
│   └── prowlarr.db
└── [other services]/config/

VOLUME_DOWNLOADS/
├── complete/                   # Finished downloads
├── incomplete/                 # In-progress downloads
└── watch/                      # Torrent watch folder

VOLUME_MEDIA/  
├── movies/                     # Movie library
├── tv/                         # TV show library
├── books/                      # Book library
└── music/                      # Music library (if used)
```

### Configuration Persistence

#### Container Configuration
- **Config files**: Stored in host volumes
- **Database files**: Persistent across restarts  
- **Log files**: Rotated and preserved
- **Application state**: Maintained in volumes

#### Environment Configuration
- **.env file**: Contains all user settings
- **compose.yaml**: Defines service architecture
- **Scripts**: Keepalive client and server logic

## 🔧 Implementation Details

### Docker Compose Structure

```yaml
# Network definitions
networks:
  vpn-network:
    driver: bridge
    ipam:
      config:
        - subnet: 10.50.0.0/16
  monitoring-network:
    driver: bridge  
    ipam:
      config:
        - subnet: 10.51.0.0/16

# Service definitions with network relationships
services:
  gluetun:                      # VPN gateway
    networks:
      - vpn-network
    
  qbittorrent:                  # Uses gluetun networking
    network_mode: service:gluetun
    depends_on:
      - gluetun
      
  keepalive-server:             # Isolated monitoring
    networks:
      - monitoring-network
```

### VPN Integration Pattern

```bash
# network_mode: service:gluetun pattern ensures:
# 1. Container shares Gluetun's network namespace
# 2. All traffic routed through VPN
# 3. localhost communication between containers
# 4. DNS provided by Gluetun
# 5. No direct internet access
```

### API Design

#### RESTful Endpoints
```python
# Keepalive server API
POST /keepalive        # Receive client status
GET  /status          # Server and client status  
GET  /health          # Health check
GET  /clients         # Detailed client info
```

#### Status Data Schema
```json
{
  "client_id": "string",
  "timestamp": "ISO8601",
  "public_ip": "string", 
  "location": {
    "country": "string",
    "region": "string", 
    "city": "string"
  },
  "dns_status": "secure|leaked",
  "provider": "string",
  "timezone": "string"
}
```

## 🎯 Design Patterns

### Network Isolation Pattern
- **Separate networks** for different purposes
- **Service-based networking** for VPN integration
- **API communication** across network boundaries
- **Security boundaries** enforced by Docker networking

### Monitoring Pattern  
- **Inside-out monitoring** (client in VPN reports to external server)
- **Change detection** via state comparison
- **Threshold-based alerting** to prevent noise
- **Multiple data sources** for validation

### Configuration Pattern
- **Environment-based config** via .env files
- **Compose variable substitution** for templating  
- **Volume mounting** for persistence
- **Secret management** via environment variables

---

**Related Documentation:**
- **[[Installation Guide]]** - Setting up the architecture  
- **[[VPN Monitoring]]** - Understanding the monitoring system
- **[[Troubleshooting]]** - Debugging architecture issues
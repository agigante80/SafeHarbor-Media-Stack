# VPN Monitoring & DNS Leak Detection

Comprehensive guide to the VPN monitoring system, DNS leak detection capabilities, and status tracking features.

## 🛡️ Overview

SafeHarbor includes a sophisticated monitoring system that:
- **Continuously monitors VPN connection** status
- **Detects DNS leaks** automatically using multiple methods
- **Tracks IP changes** and location information
- **Sends real-time alerts** via Telegram
- **Provides detailed logging** for troubleshooting

## 🏗️ Monitoring Architecture

```
┌─────────────────────────────────────────┐
│             VPN Network                 │
│  ┌─────────────────────────────────────┐│
│  │         Keepalive Client           ││
│  │      (Inside VPN Tunnel)           ││
│  │                                    ││
│  │  • Gathers VPN IP info             ││
│  │  • Tests DNS resolution            ││  
│  │  • Detects location changes        ││
│  │  • Sends status to server          ││
│  └─────────────────────────────────────┘│
└─────────────────────────────────────────┘
                    │
                    │ HTTP POST /keepalive
                    ▼
┌─────────────────────────────────────────┐
│           Host Network                  │
│  ┌─────────────────────────────────────┐│
│  │       Keepalive Server             ││
│  │     (Real IP Network)              ││
│  │                                    ││
│  │  • Receives client data            ││
│  │  • Compares with previous state    ││
│  │  • Sends Telegram notifications    ││
│  │  • Provides status API             ││
│  └─────────────────────────────────────┘│
└─────────────────────────────────────────┘
```

## 📊 VPN Status Monitoring

### Real-time Connection Tracking

The system tracks multiple VPN parameters:

**Connection Status:**
- VPN gateway connectivity
- Public IP address and changes
- Geographic location (country, region, city)
- ISP information
- Connection uptime

**Network Health:**
- DNS resolution testing
- Latency measurements  
- Bandwidth availability
- Service accessibility

### Status Collection Process

**Every 5 minutes (configurable), the client:**

1. **Gathers VPN Information:**
```bash
# External IP detection
curl -s https://ipinfo.io/json

# DNS location testing  
curl -s https://1.1.1.1/cdn-cgi/trace

# Provider information
# Geographic location data
```

2. **Performs Health Checks:**
```bash
# Test connectivity to various services
curl -s https://httpbin.org/ip
curl -s https://icanhazip.com
```

3. **Sends Status Report:**
```json
{
  "client_id": "synology-vpn-media",
  "timestamp": "2025-10-12T15:30:15Z",
  "public_ip": "185.170.104.53",
  "location": {
    "country": "PL",
    "region": "Kujawsko-Pomorskie", 
    "city": "Toruń"
  },
  "dns_status": "secure",
  "provider": "AS50599 DATASPACE P.S.A."
}
```

## 🔒 DNS Leak Detection

### What is a DNS Leak?

A DNS leak occurs when DNS queries bypass the VPN tunnel and use your ISP's DNS servers, potentially exposing:
- Websites you visit
- Your real location
- Browsing patterns
- Service usage

### Detection Methods

SafeHarbor uses multiple detection techniques:

#### Method 1: Geographic Comparison
```bash
# Get VPN server location
VPN_COUNTRY=$(curl -s https://ipinfo.io/json | jq -r '.country')

# Get DNS resolver location  
DNS_COUNTRY=$(curl -s https://1.1.1.1/cdn-cgi/trace | grep '^loc=' | cut -d'=' -f2)

# Compare locations
if [ "$VPN_COUNTRY" != "$DNS_COUNTRY" ]; then
    echo "DNS LEAK DETECTED: VPN=$VPN_COUNTRY, DNS=$DNS_COUNTRY"
fi
```

#### Method 2: DNS Server Analysis
```bash
# Check configured DNS servers
cat /etc/resolv.conf

# Should show VPN DNS (usually 127.0.0.1 for Gluetun)
# nameserver 127.0.0.1

# NOT your ISP's DNS servers like:
# nameserver 8.8.8.8
# nameserver 1.1.1.1
```

#### Method 3: External Leak Testing
```bash
# Use external DNS leak detection services
curl -s "https://bash.ws/dnsleak"

# Multiple endpoint testing
curl -s "https://ipleak.net/json/"
```

### Leak Detection in Action

**Normal Operation (No Leak):**
```log
✅ Keepalive sent successfully at Sun Oct 12 10:50:27 UTC 2025
   📍 Location: Toruń, Kujawsko-Pomorskie, PL
   🌐 VPN IP: 185.170.104.53
   🔒 DNS: PL (WAW) - No leak detected
```

**DNS Leak Detected:**
```log
⚠️ Keepalive sent with DNS LEAK at Sun Oct 12 10:55:30 UTC 2025
   📍 Location: Toruń, Kujawsko-Pomorskie, PL
   🌐 VPN IP: 185.170.104.53
   🔒 DNS: US (LAX) - LEAK DETECTED!
```

**Telegram Alert for DNS Leak:**
```
🔒 DNS LEAK DETECTED! 🔒
Client: synology-vpn-media
Time: 2025-10-12 15:25:10 (Europe/Madrid)
VPN Location: Poland (PL)
DNS Location: United States (US)
⚠️ Your DNS requests may be exposed!
```

## 🚨 Alert System

### Alert Types

#### Connection Status Alerts

**VPN Disconnection:**
- Triggered when client fails to report for >5 minutes
- Immediate notification with real IP detection
- Includes location and ISP information

**VPN Restoration:**  
- Sent when client reconnects after being offline
- Confirms VPN protection is restored
- Includes new VPN server information

#### Network Change Alerts

**IP Address Changes:**
```
🔄 VPN IP ADDRESS CHANGED 🔄
Client: synology-vpn-media
Time: 2025-10-12 16:15:30 (Europe/Madrid)
Previous IP: 185.170.104.53 (Poland)
New IP: 185.72.199.129 (Poland)  
Location: Toruń, Kujawsko-Pomorskie, PL
DNS Status: Secure (no leak detected)
```

**Location Changes:**
```
📍 VPN LOCATION CHANGED 📍
Client: synology-vpn-media
Previous: Poland, Toruń
New: Netherlands, Amsterdam
IP: 91.236.55.128
Time: 2025-10-12 16:20:15 (Europe/Madrid)
```

#### System Health Alerts

**No Clients Reporting:**
```
⚠️ NO VPN CLIENTS REPORTING ⚠️
Time: 2025-10-12 15:40:00 (Europe/Madrid)
Last client seen: 8 minutes ago
Status: All clients appear offline
Check your VPN containers immediately!
```

### Alert Configuration

```bash
# Timing settings in .env
ALERT_THRESHOLD_MINUTES=5     # Alert delay
CHECK_INTERVAL_MINUTES=5      # Check frequency  

# Notification settings
TELEGRAM_BOT_TOKEN=your_token
TELEGRAM_CHAT_ID=your_chat_id
```

## 🔍 Manual Testing & Verification

### VPN Status Commands

```bash
# Check current VPN IP and location
docker exec gluetun wget -qO- https://ipinfo.io/json

# Expected VPN response:
{
  "ip": "185.170.104.53",
  "city": "Toruń",
  "region": "Kujawsko-Pomorskie", 
  "country": "PL",
  "org": "AS50599 DATASPACE P.S.A.",
  "timezone": "Europe/Warsaw"
}

# Check if it matches your real location (should NOT match)
curl -s https://ipinfo.io/json  # From host (should show real IP)
```

### DNS Leak Testing

```bash
# Method 1: Compare VPN and DNS locations
VPN_COUNTRY=$(docker exec gluetun wget -qO- https://ipinfo.io/json | grep country | cut -d'"' -f4)
DNS_COUNTRY=$(docker exec gluetun wget -qO- https://1.1.1.1/cdn-cgi/trace | grep '^loc=' | cut -d'=' -f2)
echo "VPN Country: $VPN_COUNTRY, DNS Country: $DNS_COUNTRY"

# Should match if no leak (e.g., both show "PL")
# LEAK if different (e.g., VPN="PL", DNS="US")

# Method 2: Check DNS servers in use
docker exec gluetun cat /etc/resolv.conf

# Should show Gluetun DNS:
# nameserver 127.0.0.1

# Method 3: External leak test
docker exec gluetun wget -qO- "https://bash.ws/dnsleak"
```

### Connection Health Tests

```bash
# Test container connectivity through VPN
docker exec qbittorrent curl -s http://localhost:9117  # Jackett
docker exec sonarr curl -s https://ipinfo.io/ip        # Should show VPN IP

# Test VPN gateway health  
docker exec gluetun wget -qO- http://localhost:9999/health

# Monitor VPN logs in real-time
docker logs gluetun --follow
```

## 📋 Log Analysis

### Keepalive Client Logs

**Successful Operation:**
```log
🔄 Starting continuous VPN monitoring loop...
🔍 Gathering VPN information...
✅ Keepalive sent successfully at Sun Oct 12 10:50:27 UTC 2025
   📍 Location: Toruń, Kujawsko-Pomorskie, PL
   🌐 VPN IP: 185.170.104.53
   🏢 Provider: AS50599 DATASPACE P.S.A.
   🕒 Timezone: Europe/Warsaw
   🔒 DNS: PL (WAW) - No leak detected
⏳ Waiting 300 seconds until next keepalive...
```

**Connection Problems:**
```log
❌ Failed to send keepalive to http://duevite.eu:5421 at Sun Oct 12 10:46:01 UTC 2025
   📍 Location: Unknown, Unknown, Unknown
   🌐 VPN IP: unknown
   🏢 Provider: Unknown
   🔒 DNS: Unknown
```

### Keepalive Server Logs

**Normal Operation:**
```log
🌐 API Access: keepalive | IP: 10.51.0.1 | Auth: WITH_KEY | Status: 200_OK
Keepalive received from synology-vpn-media - IP: 185.170.104.53  
✅ Telegram message sent successfully
🔍 Monitoring check: 1 client(s) registered
```

**Alert Triggers:**
```log
⚠️ Client synology-vpn-media is offline (7 minutes since last contact)
📤 VPN disconnection alert sent to Telegram
✅ Telegram message sent successfully
```

### Gluetun VPN Logs

**Healthy VPN Connection:**
```log
INFO [firewall] allowing VPN connection...
INFO [openvpn] OpenVPN 2.6.11 x86_64-alpine-linux-musl
INFO [openvpn] Initialization Sequence Completed
INFO [healthcheck] healthy!
INFO [ip getter] Public IP address is 185.170.104.53 (Poland, Toruń)
INFO [dns] DNS server listening on [::]:53
INFO [dns] ready
```

## 🛠️ Advanced Monitoring

### Custom Alert Thresholds

```bash
# Faster alerting (2 minutes)
ALERT_THRESHOLD_MINUTES=2

# More frequent checking (every 2 minutes)  
CHECK_INTERVAL_MINUTES=2

# Less sensitive (10 minutes)
ALERT_THRESHOLD_MINUTES=10
CHECK_INTERVAL_MINUTES=10
```

### Multiple Client Monitoring

The server can track multiple VPN clients:

```bash
# Different client configurations
CLIENT_ID=home-server     # Client 1
CLIENT_ID=office-vpn      # Client 2  
CLIENT_ID=mobile-client   # Client 3
```

**Multi-client status:**
```
📊 VPN Status

🟢 home-server - Online
   IP: 185.170.104.53 (Poland)
   Last seen: 1 minute ago

🟢 office-vpn - Online  
   IP: 91.236.55.128 (Netherlands)
   Last seen: 2 minutes ago

🔴 mobile-client - Offline
   Last seen: 8 minutes ago
```

### API Endpoints

The monitoring server provides REST API endpoints:

```bash
# Get server status
curl -s http://localhost:5421/status | jq

# Get detailed client information
curl -s http://localhost:5421/clients | jq

# Health check endpoint
curl -s http://localhost:5421/health
```

## 🔐 Security Features

### API Authentication
```bash
# API key protection for keepalive endpoints
KEEPALIVE_API_KEY=your-secure-random-key

# Rate limiting (30 requests per minute per IP)
MAX_REQUESTS_PER_MINUTE=30

# IP whitelisting (optional)
ALLOWED_IPS=10.51.0.1,192.168.1.100
```

### Network Isolation
- **VPN Network**: Media containers + keepalive client
- **Monitoring Network**: Keepalive server (isolated)
- **No cross-network communication** except via API

### Data Privacy
- **No sensitive data logged** (only IP geolocation)
- **Telegram communications encrypted** (HTTPS)
- **Local data only** (no external data collection)

---

**Next Steps:**
- **[[Troubleshooting]]** - If monitoring isn't working properly
- **[[Telegram Bot]]** - Setting up notifications  
- **[[Architecture]]** - Understanding the technical implementation
# FAQ - Frequently Asked Questions

Common questions and answers about SafeHarbor Media Stack setup, configuration, and troubleshooting.

## 🚀 Getting Started

### Q: What is SafeHarbor Media Stack?
**A:** SafeHarbor is a complete Docker-based media management solution that routes all traffic through a VPN for privacy. What makes it unique is the **revolutionary integration with [VPNSentinel](https://github.com/agigante80/VPNSentinel)** - a professional open-source VPN monitoring solution with client/server architecture + live Telegram notifications that creates a bulletproof system detecting VPN issues and instantly alerting you. It includes download clients (qBittorrent), media managers (Sonarr, Radarr), indexers (Prowlarr), and this game-changing monitoring system.

### Q: Do I need technical knowledge to use this?
**A:** Basic Docker and command-line knowledge is helpful, but the detailed guides make it accessible to beginners. Most users can follow the step-by-step installation guide successfully.

### Q: Which platforms are supported?
**A:** SafeHarbor runs on:
- **Linux** (Ubuntu, Debian, CentOS, etc.)
- **Synology NAS** (DSM 6.2+ with Docker)
- **Windows** (with WSL2 and Docker Desktop)
- **macOS** (with Docker Desktop)
- **Raspberry Pi** (ARM64 support)

### Q: Can I run this on a VPS or cloud server?
**A:** Yes, but be aware of your provider's terms of service regarding torrenting. Most VPS providers prohibit torrent traffic. Consider using a dedicated server or seedbox service instead.

## 🔐 VPN and Security

### Q: Why do I need a VPN for this?
**A:** The VPN provides:
- **Privacy protection** from ISP monitoring
- **IP address masking** for download activities
- **Geo-restriction bypass** for some indexers
- **Legal protection** in restrictive jurisdictions

### Q: What makes SafeHarbor's monitoring different from other media stacks?
**A:** SafeHarbor features a **unique integration with [VPNSentinel](https://github.com/agigante80/VPNSentinel)** that no other media stack offers:

**🔧 VPNSentinel Client/Server System:**
- **Client** monitors VPN health from inside the protected network
- **Server** receives reports and coordinates responses from your real IP
- **Continuous detection** of DNS leaks, connection failures, and networking issues
- **Web dashboard** for visual monitoring at port 8080
- **API endpoints** for external system integration

**📱 Telegram Notification System:**
- **Instant mobile alerts** when any issue is detected
- **Interactive commands** (`/ping`, `/status`, `/help`) for remote monitoring
- **Smart log analysis** that provides meaningful, actionable alerts

**⚡ The Magic:** These systems work together - VPNSentinel detects technical issues, Telegram instantly notifies you with human-friendly alerts. Most other stacks fail silently, leaving you exposed without knowing.

### Q: Which VPN providers work with SafeHarbor?
**A:** 25+ providers are supported via Gluetun, including:
- **Popular**: PrivateVPN, NordVPN, ExpressVPN, Surfshark
- **Privacy-focused**: Mullvad, ProtonVPN, IVPN
- **Budget-friendly**: Windscribe, TunnelBear, HideMyAss

See the [Gluetun provider list](https://github.com/qdm12/gluetun/wiki) for complete compatibility.

### Q: What happens if my VPN disconnects?
**A:** SafeHarbor includes multiple protection layers:
- **Kill switch**: Traffic stops if VPN fails
- **Network isolation**: Download clients can't access internet without VPN
- **Auto-reconnection**: Gluetun automatically reconnects
- **Monitoring alerts**: Telegram notifications for connection issues

### Q: Can I use SafeHarbor's monitoring system with my existing Docker setup?
**A:** **Absolutely!** This is one of SafeHarbor's key advantages. **VPNSentinel is a standalone project** that can be deployed independently with any Docker VPN setup:

```yaml
# Add to your existing docker-compose.yml
services:
  # Your existing VPN container (gluetun, transmission-vpn, etc.)
  your-vpn:
    # ... your existing VPN config ...

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
    network_mode: "container:your-vpn"  # Monitor your VPN
    depends_on: [your-vpn]
```

**Benefits:** Drop-in monitoring, no changes to existing setup, universal VPN support, instant mobile alerts.

**Learn more:** [VPNSentinel Project](https://github.com/agigante80/VPNSentinel)

### Q: Can I use my existing VPN app alongside SafeHarbor?
**A:** Not recommended. Running two VPN clients can cause conflicts. SafeHarbor's containerized VPN only affects the media stack, not your entire system.

### Q: How do I verify my VPN is working?
**A:** Check your VPN IP:
```bash
docker exec gluetun wget -qO- https://ipinfo.io/json
```
The IP should match your VPN server location, not your real location.

## 🏠 Hardware and System Requirements

### Q: What are the minimum system requirements?
**A:**
- **CPU**: Dual-core 2GHz+ (Intel/AMD x64)
- **RAM**: 4GB minimum, 8GB+ recommended  
- **Storage**: 50GB+ free space
- **Network**: Stable broadband connection

### Q: Can I run this on a Raspberry Pi?
**A:** Yes! Raspberry Pi 4 with 4GB+ RAM works well. Use external storage for downloads and consider adjusting memory limits for containers.

### Q: How much bandwidth does SafeHarbor use?
**A:** Depends on your usage:
- **API calls**: <1GB/month for *arr applications
- **Downloads**: Whatever you configure in qBittorrent
- **Monitoring**: <100MB/month for Telegram bot

### Q: Can I run SafeHarbor on the same machine as Plex/Jellyfin?
**A:** Absolutely! SafeHarbor organizes media files that Plex/Jellyfin can read. Just ensure your media folders are accessible to both systems.

## ⚙️ Configuration and Setup

### Q: Do I need to configure each service manually?
**A:** Initial setup requires configuring indexers and download clients in each *arr app, but Prowlarr simplifies this by syncing indexers to all applications automatically.

### Q: Can I use existing Sonarr/Radarr configurations?
**A:** Yes! You can migrate existing configurations by copying config files to the appropriate directories before starting SafeHarbor.

### Q: How do I change the web interface ports?
**A:** Edit the port mappings in `compose.yaml`:
```yaml
qbittorrent:
  ports:
    - "9802:8085"  # Change 9802 to your preferred port
```

### Q: Can I add more applications to the stack?
**A:** Yes! SafeHarbor is designed to be extensible. You can add services like:
- **Bazarr** (subtitles)
- **Lidarr** (music)
- **LazyLibrarian** (books)
- **Ombi/Overseerr** (requests)

**Bonus:** VPNSentinel monitoring will automatically monitor any new services you add to the VPN network, providing the same instant failure notifications.

### Q: Where are my configuration files stored?
**A:** All configurations are stored in the paths defined by `VOLUME_DOCKER_PROJECT` in your `.env` file. Each service has its own subdirectory (e.g., `sonarr/config/`, `radarr/config/`).

## 📱 Telegram Integration

### Q: Is Telegram monitoring required?
**A:** No, it's optional but highly recommended. The monitoring provides:
- VPN connection status alerts
- Download completion notifications  
- System health monitoring
- Remote control commands

### Q: How do I set up the Telegram bot?
**A:** 
1. Message [@BotFather](https://t.me/botfather) on Telegram
2. Send `/newbot` and follow prompts
3. Copy the bot token to your `.env` file
4. Get your chat ID from [@userinfobot](https://t.me/userinfobot)
5. Add both values to `.env` and restart the stack

### Q: What commands does the Telegram bot support?
**A:** Available commands:
- `/ping` - Test bot connectivity
- `/status` - System and VPN status
- `/help` - List all commands
- Text messages - Log analysis and alerts

### Q: Can multiple people use the same Telegram bot?
**A:** The bot responds to the configured chat ID only. For multiple users, you can:
- Create a Telegram group and add the bot
- Use the group chat ID in configuration
- All group members can interact with the bot

### Q: I don't have a static IP address. Can I still use SafeHarbor's monitoring system?
**A:** **Yes!** There are several solutions for dynamic IP addresses:

**🌐 Dynamic DNS (DDNS) Services:**
- **DuckDNS** (free) - `yourname.duckdns.org`
- **No-IP** (free tier available) - `yourname.ddns.net`
- **Dynu** (free) - `yourname.dynu.net`
- **Cloudflare** (free with domain) - `monitor.yourdomain.com`

**🏠 Router-based DDNS:**
- Most routers support built-in DDNS clients
- Automatically updates your IP when it changes
- Works with services like DuckDNS, No-IP

**☁️ Custom Domain Solutions:**
- **Cloudflare Tunnel** (free) - Secure tunnel without port forwarding
- **Ngrok** (free tier) - Secure tunnels to localhost
- **Tailscale** - Mesh VPN for secure access

**📱 Configuration Example:**
```bash
# In your .env file, use DDNS hostname instead of IP
VPN_SENTINEL_SERVER_URL=https://yourname.duckdns.org:5421

# Or with custom domain
VPN_SENTINEL_SERVER_URL=https://monitor.yourdomain.com:5421
```

**💡 Pro Tip:** Cloudflare Tunnel is recommended for maximum security as it doesn't require port forwarding and provides SSL certificates automatically.

## 🔧 Troubleshooting

### Q: My containers keep restarting, what's wrong?
**A:** Common causes:
1. **VPN authentication failure** - Check credentials in `.env`
2. **Permission issues** - Verify PUID/PGID settings match your user
3. **Port conflicts** - Ensure no other services use the same ports
4. **Insufficient resources** - Check available RAM and CPU

### Q: Downloads aren't starting, why?
**A:** Check these items:
1. **Indexers configured** in Prowlarr and synced to *arr apps
2. **Download client** (qBittorrent) added to *arr applications
3. **VPN connection** working (can't download without VPN)
4. **Disk space** available in download directory

### Q: I can't access the web interfaces, what should I do?
**A:** Troubleshooting steps:
1. **Check container status**: `docker compose ps`
2. **Verify ports**: `netstat -tlnp | grep 9802`
3. **Check firewall**: Ensure ports 9802-9811 are open
4. **Container logs**: `docker compose logs service-name`

### Q: The VPN keeps disconnecting, how do I fix it?
**A:** Try these solutions:
1. **Change VPN protocol** from UDP to TCP (or vice versa)
2. **Switch VPN servers** by changing `SERVER_COUNTRIES`
3. **Check VPN account** for active subscription
4. **Review logs**: `docker logs gluetun --tail 50`

### Q: Media files aren't being moved to my library, why?
**A:** Common issues:
1. **Path mapping** - Ensure download and media paths match between qBittorrent and *arr apps
2. **Permissions** - Files must be readable by media server user
3. **Import settings** - Check "Completed Download Handling" in *arr apps
4. **File naming** - Ensure proper scene naming for recognition

## 💾 Data Management

### Q: Where should I store my media files?
**A:** Recommended structure:
```
/media/
├── movies/     # Radarr manages this
├── tv/         # Sonarr manages this
├── music/      # Lidarr manages this
└── books/      # Readarr manages this
```

### Q: How do I backup my SafeHarbor configuration?
**A:** Regular backups should include:
```bash
# Backup configurations
tar -czf safeharbor-backup.tar.gz \
  .env compose.yaml */config/

# Backup to cloud storage
rclone copy safeharbor-backup.tar.gz remote:backups/
```

### Q: Can I move SafeHarbor to a different machine?
**A:** Yes! The process:
1. **Stop services**: `docker compose down`
2. **Backup configurations** and `.env` file
3. **Install SafeHarbor** on new machine
4. **Restore configurations** to new location
5. **Update paths** in `.env` if needed
6. **Start services**: `docker compose up -d`

### Q: How much storage space do I need?
**A:** Planning recommendations:
- **Configurations**: 1-5GB
- **Downloads**: 50-500GB (temporary storage)
- **Media library**: Depends on collection size
- **Buffer space**: 20% extra for download peaks

## 🔄 Updates and Maintenance

### Q: How often should I update SafeHarbor?
**A:** Recommended schedule:
- **Security updates**: Immediately when available
- **Container updates**: Weekly or bi-weekly
- **SafeHarbor repository**: Monthly
- **Major versions**: Quarterly with testing

### Q: Will updates break my configuration?
**A:** SafeHarbor maintains backward compatibility, but:
- **Minor updates**: Usually seamless
- **Major updates**: May require configuration changes
- **Always backup** before updating
- **Read release notes** for breaking changes

### Q: Can I update individual containers?
**A:** Yes:
```bash
# Update single container
docker compose pull qbittorrent
docker compose up -d qbittorrent

# Update all containers  
docker compose pull && docker compose up -d
```

## 🌐 Networking and Access

### Q: Can I access SafeHarbor remotely?
**A:** Several options:
1. **VPN to home network** (most secure)
2. **Reverse proxy** with SSL certificates
3. **Cloudflare Tunnel** for secure exposure
4. **Port forwarding** (least secure, not recommended)

### Q: Why can't I access services from other devices?
**A:** Check these settings:
1. **Firewall rules** allow the ports
2. **Bind to all interfaces**: Use `0.0.0.0:port` instead of `127.0.0.1:port`
3. **Docker network** configuration allows external access
4. **Router configuration** for local network access

### Q: Can I use a custom domain name?
**A:** Yes! Options include:
- **Local DNS**: Set up Pi-hole or router DNS entries
- **Dynamic DNS**: Use services like DuckDNS or No-IP
- **Reverse proxy**: Traefik or Nginx with SSL certificates

## 🛡️ Security and Privacy

### Q: How secure is SafeHarbor?
**A:** Security features:
- **VPN isolation** prevents IP leaks
- **Container isolation** limits attack surface
- **No external exposure** by default
- **Active monitoring** - VPNSentinel continuously verifies VPN protection
- **Instant leak detection** - Telegram alerts if your real IP is exposed
- **DNS leak protection** - continuous verification that queries don't leak
- **Regular updates** for security patches

### Q: Should I expose SafeHarbor to the internet?
**A:** **Not recommended** without proper security:
- Use VPN access to your home network instead
- If you must expose services, use:
  - Strong authentication (2FA where possible)
  - SSL certificates
  - Fail2ban or similar protection
  - Regular security monitoring

### Q: What data does SafeHarbor collect?
**A:** SafeHarbor itself collects no data. Individual services may log:
- **Local logs** only (not sent anywhere)
- **API requests** to indexers and trackers
- **VPN connection logs** (varies by provider)

### Q: How do I change default passwords?
**A:** Change defaults immediately:
- **qBittorrent**: Default is `admin`/`adminadmin`
- **Other services**: Usually no default password, set one in settings
- **Use strong, unique passwords** for each service

## 💡 Performance and Optimization

### Q: SafeHarbor is running slowly, how can I improve performance?
**A:** Optimization tips:
1. **Allocate more RAM** if available
2. **Use SSD storage** for configurations
3. **Limit concurrent downloads** in qBittorrent
4. **Adjust indexer refresh intervals** in *arr apps
5. **Enable hardware acceleration** where supported

### Q: Can I limit resource usage?
**A:** Yes, add resource limits to `compose.yaml`:
```yaml
services:
  qbittorrent:
    deploy:
      resources:
        limits:
          memory: 1G
          cpus: '1.0'
```

### Q: How many torrents can SafeHarbor handle simultaneously?
**A:** Depends on your hardware:
- **Basic system**: 5-20 active torrents
- **Mid-range**: 20-100 active torrents  
- **High-end**: 100+ active torrents
- **Monitor performance** and adjust limits as needed

## 🆘 Getting Help

### Q: Where can I get help if I'm stuck?
**A:** Support resources:
1. **[[Troubleshooting]]** guide for common issues
2. **GitHub Issues** for bug reports and feature requests
3. **Community forums** for user discussions
4. **Documentation** for detailed guides

### Q: How do I report a bug?
**A:** When reporting issues:
1. **Check existing issues** first
2. **Provide system information** (OS, Docker version)
3. **Include relevant logs** from `docker compose logs`
4. **Describe steps to reproduce** the problem
5. **Use issue templates** on GitHub

### Q: Does SafeHarbor's monitoring work with other VPN solutions?
**A:** **Yes!** **VPNSentinel is a standalone project** designed to work with **any Docker VPN container**, including:
- **Gluetun** (recommended)
- **transmission-openvpn**
- **qbittorrent-vpn**
- **Custom VPN containers**

Simply deploy VPNSentinel alongside your existing VPN setup - no modifications to your current stack required.

**Learn more:** [VPNSentinel Project](https://github.com/agigante80/VPNSentinel)

### Q: Can I contribute to SafeHarbor?
**A:** Absolutely! Contributions welcome:
- **Documentation improvements**
- **Bug fixes and features** 
- **Testing on different platforms**
- **Community support**
- **Monitoring system enhancements** - help improve VPNSentinel integration or contribute to [VPNSentinel](https://github.com/agigante80/VPNSentinel)

See the contribution guidelines on GitHub for details.

---

**Still have questions?**
- Check the **[[Troubleshooting]]** guide
- Search existing **[GitHub Issues](https://github.com/agigante80/SafeHarbor-Media-Stack/issues)**
- Open a **[new issue](https://github.com/agigante80/SafeHarbor-Media-Stack/issues/new)** if your question isn't answered

**Quick Links:**
- **[[Installation Guide]]** - Getting started
- **[[Configuration]]** - Advanced settings  
- **[[Updates]]** - Keeping current
- **[[Synology Guide]]** - NAS-specific setup
# Welcome to SafeHarbor Media Stack

SafeHarbor Media Stack is a complete Docker Compose setup for automated media management with VPN protection, featuring popular *arr applications, qBittorrent, and intelligent monitoring with Telegram notifications.

## 🎯 What is SafeHarbor Media Stack?

A comprehensive, security-focused media automation platform that combines:
- **VPN-Protected Downloading**: All traffic routed through Gluetun VPN
- **Automated Media Management**: Complete *arr ecosystem (Sonarr, Radarr, etc.)
- **Intelligent Monitoring**: Real-time VPN status with DNS leak detection
- **Telegram Integration**: Instant notifications and bot commands
- **Production Ready**: Tested on various platforms including Synology NAS

## 🏗️ Architecture Overview

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

## 📚 Documentation Pages

### 🚀 Getting Started
- **[[Installation Guide]]** - Complete setup from scratch
- **[[Configuration]]** - Environment variables and customization
- **[[Synology Guide]]** - NAS-specific installation steps

### 🔧 Setup & Configuration  
- **[[Telegram Bot]]** - Bot setup and interactive commands
- **[[VPN Monitoring]]** - DNS leak detection and status tracking
- **[[Architecture]]** - Technical deep-dive and networking details

### 🛠️ Support & Maintenance
- **[[Troubleshooting]]** - Common issues and real-world solutions
- **[[Updates]]** - Keeping your stack current
- **[[FAQ]]** - Frequently asked questions

## ✨ Key Features

### 🔒 Security First
- **VPN Protection**: All media traffic routed through VPN (25+ providers)
- **DNS Leak Detection**: Automatic monitoring and alerting
- **Network Isolation**: Separated monitoring and media networks
- **API Authentication**: Secure keepalive system with rate limiting

### 📺 Complete Media Stack
- **[Sonarr](https://sonarr.tv/)** - TV Show automation
- **[Radarr](https://radarr.video/)** - Movie automation  
- **[Readarr](https://readarr.com/)** - Book automation
- **[Bazarr](https://bazarr.media/)** - Subtitle automation
- **[Prowlarr](https://prowlarr.com/)** - Indexer management
- **[Jackett](https://github.com/Jackett/Jackett)** - Torrent indexer proxy
- **[qBittorrent](https://qbittorrent.org/)** - Download client

### 🤖 Smart Monitoring
- **Real-time VPN Status**: Continuous monitoring with Telegram alerts
- **IP Change Detection**: Automatic notifications when VPN IP changes
- **Connection Failure Alerts**: Immediate warnings when VPN drops
- **Interactive Bot**: `/ping`, `/status`, `/help` commands
- **Location Tracking**: Detailed geolocation information

### 🌐 Platform Compatibility
- **Docker Compose**: Works on any Docker-compatible system
- **Synology NAS**: Specifically tested and optimized
- **Linux Servers**: Ubuntu, Debian, CentOS, etc.
- **Windows**: WSL2 and Docker Desktop support
- **macOS**: Docker Desktop compatibility

## 🎯 Use Cases

### Home Media Server
Perfect for building a complete automated media acquisition and management system while maintaining privacy through VPN protection.

### Privacy-Focused Setup
Ideal for users who prioritize security and want comprehensive monitoring of their VPN connection status.

### Learning Platform  
Excellent for understanding Docker networking, VPN integration, and automated system monitoring concepts.

## ⚠️ Important Legal Notice

**This is an EDUCATIONAL PROJECT** created for technical demonstration purposes.

- Review local laws regarding VPN usage and content downloading
- Ensure compliance with applicable regulations in your jurisdiction
- Support content creators and legal distribution platforms
- Use responsibly and at your own risk

## 🤝 Community & Support

- **GitHub Issues**: Report bugs and request features
- **GitHub Discussions**: Community support and ideas
- **Wiki**: Comprehensive documentation and guides
- **Pull Requests**: Contribute improvements and fixes

## 🚀 Quick Links

- **[[Installation Guide]]** - Get started in 10 minutes
- **[[Troubleshooting]]** - Solve common issues
- **[[Telegram Bot]]** - Setup monitoring notifications
- **[GitHub Repository](https://github.com/agigante80/SafeHarbor-Media-Stack)** - Source code
- **[Issues](https://github.com/agigante80/SafeHarbor-Media-Stack/issues)** - Report problems

---

**Ready to get started?** Head to the **[[Installation Guide]]** for step-by-step setup instructions!
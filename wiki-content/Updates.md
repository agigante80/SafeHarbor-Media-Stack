# Updates - Keeping Your Stack Current

Complete guide for maintaining and updating SafeHarbor Media Stack to ensure security, performance, and access to latest features.

## 🔄 Update Strategy

SafeHarbor Media Stack follows a **rolling release** model with regular updates for:
- **Security patches** - Applied immediately
- **Feature updates** - Monthly releases  
- **Container updates** - Automatic with configurable policies
- **Major versions** - Quarterly with migration guides

### Update Philosophy
- **Stability first** - Thoroughly tested updates
- **Backward compatibility** - Smooth upgrade paths
- **Rollback support** - Easy recovery from issues
- **Minimal downtime** - Hot-swappable updates where possible

## 📅 Update Schedule

### Automatic Updates (Recommended)
```yaml
# In compose.yaml - Watchtower auto-updates
watchtower:
  image: containrrr/watchtower:latest
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock
  environment:
    - WATCHTOWER_CLEANUP=true
    - WATCHTOWER_SCHEDULE=0 0 2 * * SUN  # Every Sunday at 2 AM
    - WATCHTOWER_NOTIFICATIONS=slack
    - WATCHTOWER_NOTIFICATION_SLACK_HOOK_URL=${SLACK_WEBHOOK}
  restart: unless-stopped
```

### Manual Update Cycle
```bash
# Weekly: Check for container updates
# Bi-weekly: Update SafeHarbor repository
# Monthly: Review and update configurations
# Quarterly: Major version updates and cleanup
```

## 🔧 Container Updates

### Check for Updates
```bash
# Check current versions
docker images | grep -E "(gluetun|qbittorrent|sonarr|radarr|prowlarr)"

# Check for available updates
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower --run-once --cleanup --debug
```

### Manual Container Updates
```bash
# Update specific container
docker compose pull gluetun
docker compose up -d gluetun

# Update all containers
docker compose pull
docker compose up -d

# Check updated versions
docker compose ps
```

### Rollback Container Updates
```bash
# Tag current version before update
docker tag gluetun:latest gluetun:backup-$(date +%Y%m%d)

# Rollback if needed
docker compose stop gluetun
docker tag gluetun:backup-20241012 gluetun:latest
docker compose up -d gluetun
```

## 📦 SafeHarbor Repository Updates

### Check for New Releases
```bash
# Check current version
git describe --tags --abbrev=0

# Check remote for updates
git fetch origin
git log HEAD..origin/main --oneline

# See available releases
curl -s https://api.github.com/repos/agigante80/SafeHarbor-Media-Stack/releases/latest | \
  jq -r '.tag_name'
```

### Update SafeHarbor Stack
```bash
# Backup current configuration
cp .env .env.backup.$(date +%Y%m%d)
cp compose.yaml compose.yaml.backup.$(date +%Y%m%d)

# Stop services (optional - for major updates)
docker compose down

# Update repository
git pull origin main

# Check for breaking changes
git log --oneline --grep="BREAKING"

# Restart services
docker compose up -d
```

### Handle Configuration Changes
```bash
# Compare configurations
diff .env.backup.20241012 .env.example

# Merge new settings
# Add new required variables from .env.example to .env
# Keep your existing customizations

# Validate configuration
docker compose config
```

## 🔐 Security Updates

### Critical Security Updates
SafeHarbor follows **responsible disclosure** and provides:
- **Immediate patches** for critical vulnerabilities
- **Security advisories** via GitHub releases
- **CVE tracking** for all components
- **Automated scanning** with vulnerability reports

### Security Update Process
```bash
# Check for security advisories
curl -s https://api.github.com/repos/agigante80/SafeHarbor-Media-Stack/releases | \
  jq -r '.[] | select(.name | contains("Security")) | .name'

# Emergency security update
git fetch origin
git checkout origin/security-patch-branch  # If available
docker compose pull
docker compose up -d
```

### Container Security Scanning
```bash
# Scan containers for vulnerabilities
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/trivy image gluetun:latest

# Scan all SafeHarbor containers
for container in gluetun qbittorrent sonarr radarr prowlarr readarr bazarr; do
  echo "Scanning $container..."
  docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
    aquasec/trivy image $container:latest
done
```

## 🎯 Feature Updates

### New Feature Integration
```bash
# Check changelog for new features
curl -s https://api.github.com/repos/agigante80/SafeHarbor-Media-Stack/releases/latest | \
  jq -r '.body'

# Review new environment variables
diff .env .env.example | grep "^>"

# Test new features in staging
cp .env .env.staging
# Modify staging settings and test
docker compose -f compose.yaml -f compose.staging.yaml up -d
```

### Optional Component Updates
```bash
# Enable new optional services
# Example: Adding new indexer or monitoring tool

# Check available optional components
ls optional-components/

# Add to compose.yaml
docker compose -f compose.yaml -f optional-components/bazarr.yaml up -d
```

## 📊 Update Monitoring

### Update Notifications
```bash
# Telegram notifications for updates
# Add to your monitoring setup

# Slack webhook for update notifications  
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"SafeHarbor updated to version X.Y.Z"}' \
  $SLACK_WEBHOOK_URL

# Email notifications via sendmail/postfix
echo "SafeHarbor updated successfully" | mail -s "Update Complete" admin@example.com
```

### Update Status Dashboard
```bash
# Create update status endpoint
curl http://localhost:5421/api/status | jq '.version'

# Check last update time
docker inspect --format='{{.Created}}' gluetun

# Update history log
docker system events --filter container=gluetun --since 24h
```

## 🔄 Automated Update Pipeline

### Watchtower Configuration
```yaml
# Advanced Watchtower setup in compose.yaml
watchtower:
  image: containrrr/watchtower:latest
  container_name: watchtower
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock
  environment:
    # Update schedule (Cron format)
    - WATCHTOWER_SCHEDULE=0 0 2 * * SUN  # Sunday 2 AM
    
    # Cleanup old images
    - WATCHTOWER_CLEANUP=true
    
    # Only update specific containers
    - WATCHTOWER_SCOPE=safeharbor
    
    # Notifications
    - WATCHTOWER_NOTIFICATIONS=slack
    - WATCHTOWER_NOTIFICATION_SLACK_HOOK_URL=${SLACK_WEBHOOK}
    - WATCHTOWER_NOTIFICATION_SLACK_IDENTIFIER=SafeHarbor-Updates
    
    # Update policy
    - WATCHTOWER_POLL_INTERVAL=86400  # 24 hours
    - WATCHTOWER_TIMEOUT=10s
    
    # Rolling updates (update one at a time)
    - WATCHTOWER_NO_PULL=false
    - WATCHTOWER_NO_RESTART=false
    
    # Monitor only mode (notifications without updates)
    # - WATCHTOWER_MONITOR_ONLY=true
    
  labels:
    - com.centurylinklabs.watchtower.scope=safeharbor
  restart: unless-stopped
```

### Update Health Checks
```bash
# Health check script after updates
cat > /usr/local/bin/safeharbor-health-check.sh << 'EOF'
#!/bin/bash

echo "Starting SafeHarbor health check..."

# Check VPN connection
VPN_IP=$(docker exec gluetun wget -qO- https://ipinfo.io/ip 2>/dev/null)
if [[ $? -eq 0 && ! -z "$VPN_IP" ]]; then
  echo "✅ VPN: Connected ($VPN_IP)"
else
  echo "❌ VPN: Connection failed"
  exit 1
fi

# Check container health
for container in gluetun qbittorrent sonarr radarr prowlarr; do
  if docker ps --filter "name=$container" --filter "status=running" | grep -q "$container"; then
    echo "✅ $container: Running"
  else
    echo "❌ $container: Not running"
    exit 1
  fi
done

# Check service accessibility
if curl -sf http://localhost:9802 >/dev/null; then
  echo "✅ qBittorrent: Accessible"
else  
  echo "❌ qBittorrent: Not accessible"
  exit 1
fi

# Check Telegram bot (if configured)
if [[ ! -z "$TELEGRAM_BOT_TOKEN" ]]; then
  if curl -sf "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/getMe" >/dev/null; then
    echo "✅ Telegram Bot: Active"
  else
    echo "⚠️ Telegram Bot: Not responding"
  fi
fi

echo "✅ All health checks passed!"
EOF

chmod +x /usr/local/bin/safeharbor-health-check.sh
```

## 🚨 Update Troubleshooting

### Common Update Issues

#### Container Won't Start After Update
```bash
# Check container logs
docker compose logs container-name

# Check image integrity
docker inspect image-name

# Rollback to previous version
docker compose down
docker tag image-name:backup-date image-name:latest
docker compose up -d
```

#### Configuration Conflicts
```bash
# Validate compose file
docker compose config

# Check for conflicting environment variables
docker compose config | grep -E "(environment|env_file)"

# Reset to default configuration
cp compose.yaml.example compose.yaml
# Merge your customizations back in
```

#### Network Issues After Update
```bash
# Recreate Docker networks
docker network ls | grep safeharbor
docker network prune
docker compose down
docker compose up -d
```

#### Performance Degradation
```bash
# Check resource usage
docker stats --no-stream

# Check for memory leaks
docker system df
docker system prune

# Monitor container performance
docker exec container-name top
docker exec container-name free -h
```

### Update Recovery

#### Backup Before Updates
```bash
# Automated backup script
cat > /usr/local/bin/safeharbor-backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/opt/safeharbor-backups"
DATE=$(date +%Y%m%d_%H%M%S)
PROJECT_DIR="/path/to/SafeHarbor-Media-Stack"

mkdir -p "$BACKUP_DIR"

# Backup configuration
tar -czf "$BACKUP_DIR/config-$DATE.tar.gz" \
  "$PROJECT_DIR/.env" \
  "$PROJECT_DIR/compose.yaml" \
  "$PROJECT_DIR"/*/config/

# Backup container data  
docker run --rm -v safeharbor_gluetun_data:/data \
  -v "$BACKUP_DIR:/backup" alpine \
  tar czf "/backup/gluetun-data-$DATE.tar.gz" /data

echo "Backup created: $BACKUP_DIR/config-$DATE.tar.gz"
EOF
```

#### Disaster Recovery
```bash
# Complete stack restoration
cd /path/to/SafeHarbor-Media-Stack

# Stop all services
docker compose down -v

# Restore configuration
tar -xzf /opt/safeharbor-backups/config-20241012_120000.tar.gz -C /

# Restore container data
docker run --rm -v safeharbor_gluetun_data:/data \
  -v /opt/safeharbor-backups:/backup alpine \
  tar xzf /backup/gluetun-data-20241012_120000.tar.gz -C /data

# Restart services
docker compose up -d
```

## 📈 Update Best Practices

### Staging Environment
```bash
# Create staging environment
cp .env .env.staging
cp compose.yaml compose.staging.yaml

# Modify staging configuration
sed -i 's/:9802/:19802/g' compose.staging.yaml  # Change ports
sed -i 's/safeharbor/safeharbor-staging/g' compose.staging.yaml  # Change names

# Test updates in staging first
docker compose -f compose.staging.yaml up -d
```

### Update Documentation
```bash
# Document update process
cat > UPDATE_LOG.md << 'EOF'
# SafeHarbor Update Log

## 2024-10-12 - Version 2.3.1
- Updated gluetun to v3.35.0
- Added new VPN provider support
- Fixed container health checks
- Migration notes: No configuration changes required

## 2024-09-28 - Version 2.3.0  
- Added Watchtower auto-updates
- New monitoring dashboard
- Breaking change: TELEGRAM_BOT_TOKEN format changed
- Migration: Update .env with new token format
EOF
```

### Update Testing
```bash
# Automated update testing
cat > /usr/local/bin/test-update.sh << 'EOF'
#!/bin/bash

echo "Testing SafeHarbor update..."

# Run health checks
/usr/local/bin/safeharbor-health-check.sh

# Test VPN functionality
VPN_TEST=$(docker exec gluetun wget -qO- https://ipinfo.io/country 2>/dev/null)
if [[ "$VPN_TEST" != "$(cat /opt/safeharbor/expected-country.txt)" ]]; then
  echo "❌ VPN location test failed"
  exit 1
fi

# Test download functionality  
# (Add torrent test, check *arr API responses, etc.)

echo "✅ Update tests passed!"
EOF
```

## 🎯 Version Management

### Version Tracking
```bash
# Check current versions of all components
cat > /usr/local/bin/safeharbor-versions.sh << 'EOF'
#!/bin/bash

echo "SafeHarbor Media Stack Version Report"
echo "======================================"

# Repository version
cd /path/to/SafeHarbor-Media-Stack
echo "Repository: $(git describe --tags --always)"

# Container versions
docker images --format "table {{.Repository}}\t{{.Tag}}" | grep -E "(gluetun|qbittorrent|sonarr|radarr|prowlarr)"

# Configuration version
if [[ -f ".env" ]]; then
  CONFIG_VERSION=$(grep "^# Version:" .env | cut -d' ' -f3)
  echo "Configuration: ${CONFIG_VERSION:-Unknown}"
fi

# Last update
echo "Last update: $(stat -c %y compose.yaml | cut -d' ' -f1)"
EOF
```

### Release Notes
Stay informed about updates:
- **GitHub Releases**: https://github.com/agigante80/SafeHarbor-Media-Stack/releases
- **Changelog**: Review CHANGELOG.md for detailed changes
- **Security Advisories**: GitHub Security tab for vulnerability reports
- **Community**: Discord/Reddit for update discussions

---

**Related Documentation:**
- **[[Installation Guide]]** - Fresh installation process
- **[[Configuration]]** - Advanced configuration options
- **[[Troubleshooting]]** - Update-related issues
- **[[FAQ]]** - Common update questions

**Update Support:**
Having update issues? Check the **[[Troubleshooting]]** guide or open an [issue on GitHub](https://github.com/agigante80/SafeHarbor-Media-Stack/issues) with "Update" in the title.

---

**Stay Current:** Subscribe to [GitHub releases](https://github.com/agigante80/SafeHarbor-Media-Stack/releases) or join our community for update notifications!
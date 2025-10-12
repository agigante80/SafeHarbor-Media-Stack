# Telegram Bot

Complete guide to setting up and using the SafeHarbor Telegram monitoring bot for real-time VPN status notifications.

## 🤖 Overview

The SafeHarbor Telegram bot provides:
- **Real-time VPN monitoring** with instant notifications
- **Interactive commands** for status checking
- **DNS leak detection** with automatic alerts
- **Connection failure alerts** when VPN drops
- **IP change notifications** when VPN server changes

## 🚀 Bot Setup

### Step 1: Create Telegram Bot

1. **Open Telegram** and search for [@BotFather](https://t.me/botfather)
2. **Send `/newbot`** command
3. **Choose a name**: `SafeHarbor VPN Monitor` (display name)
4. **Choose username**: `safeharbor_vpn_bot` (must end with 'bot')
5. **Save the Bot Token**: `1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ`

### Step 2: Get Your Chat ID

#### Method 1: Using @userinfobot
1. Search for [@userinfobot](https://t.me/userinfobot) 
2. Start a chat and send any message
3. Copy your **Chat ID**: `123456789`

#### Method 2: API Method
```bash
# Replace YOUR_BOT_TOKEN with your actual token
curl "https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates"

# First send a message to your bot, then run the command above
# Look for "chat":{"id":123456789} in the response
```

### Step 3: Configure Environment

Add to your `.env` file:
```bash
# Telegram Configuration
TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ
TELEGRAM_CHAT_ID=123456789
KEEPALIVE_API_KEY=your-secure-random-key-here
```

### Step 4: Test Setup

```bash
# Test bot communication
curl -X POST "https://api.telegram.org/bot1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ/sendMessage" \
     -d "chat_id=123456789&text=SafeHarbor setup test - bot is working!"
```

### Step 5: Start Monitoring

```bash
# Restart the keepalive server to load Telegram config
docker compose restart keepalive-server

# Check logs
docker compose logs -f keepalive-server
```

**Expected startup logs:**
```log
Starting Keepalive Server...
Telegram bot polling started
✅ VPN Keepalive Server Started
Server listening on port 5000
```

## 💬 Bot Commands

### Interactive Commands

| Command | Description | Response |
|---------|-------------|----------|
| `/ping` | Test bot connectivity | Server status with uptime info |
| `/status` | Get current VPN status | Detailed client status report |
| `/help` | Show available commands | Command list with descriptions |
| **Any other text** | Unknown command/text | Friendly response with guidance |

### Command Examples

#### `/ping` Command
```
User: /ping
Bot: 🏓 Pong!

     Server time: 2025-10-12 15:30:22 CEST
     Active clients: 1
     Alert threshold: 5 minutes
     Check interval: 5 minutes
```

#### `/status` Command  
```
User: /status
Bot: 📊 VPN Status

     🟢 synology-vpn-media - Online
        IP: 185.170.104.53
        Last seen: 2 minutes ago
        Time: 15:28:15 CEST

     Server time: 15:30:22 CEST
```

#### `/help` Command
```
User: /help  
Bot: ❓ VPN Keepalive Bot Help

     Available commands:
     🏓 /ping - Test bot connectivity and get server info
     📊 /status - Get detailed VPN client status
     ❓ /help - Show this help message

     Automatic notifications:
     ✅ VPN connection established
     🔄 VPN IP address changes
     ❌ VPN connection lost (after 5 minutes)
     🔒 DNS leak test results

     Monitoring every 5 minutes
```

#### Unknown Command Response
```
User: hello
Bot: 👋 Hello! I'm your VPN monitoring bot.

     I received: hello

     Use /help to see available commands.

     Available commands:
     🏓 /ping - Test connectivity
     📊 /status - Get VPN status  
     ❓ /help - Show help
```

## 🚨 Automatic Notifications

### VPN Connection Alerts

#### 🔴 VPN Disconnection Alert
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

#### ✅ VPN Restored Alert
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

#### 🔄 VPN IP Change Alert
```
🔄 VPN IP ADDRESS CHANGED 🔄
Client: synology-vpn-media
Time: 2025-10-12 16:15:30 (Europe/Madrid)
Previous IP: 185.170.104.53 (Poland)
New IP: 185.72.199.129 (Poland)
Location: Toruń, Kujawsko-Pomorskie, PL
DNS Status: Secure (no leak detected)
```

### System Monitoring Alerts

#### ⚠️ No Clients Alert
```
⚠️ NO VPN CLIENTS REPORTING ⚠️
Time: 2025-10-12 15:40:00 (Europe/Madrid)
Last client seen: 8 minutes ago
Status: All clients appear offline
Check your VPN containers immediately!
```

#### 🔍 DNS Leak Detection
```
🔒 DNS LEAK DETECTED! 🔒  
Client: synology-vpn-media
Time: 2025-10-12 15:25:10 (Europe/Madrid)
VPN Location: Poland (PL)
DNS Location: United States (US)
⚠️ Your DNS requests may be exposed!
```

## 🔧 Configuration Options

### Environment Variables

```bash
# Required
TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ
TELEGRAM_CHAT_ID=123456789

# Optional
KEEPALIVE_API_KEY=secure-random-key        # API authentication
ALERT_THRESHOLD_MINUTES=5                  # Alert delay (default: 5)
CHECK_INTERVAL_MINUTES=5                   # Check frequency (default: 5)
TZ=Europe/Madrid                          # Timezone for notifications
```

### Advanced Settings

#### Custom Alert Threshold
```bash
# Alert after 10 minutes instead of 5
ALERT_THRESHOLD_MINUTES=10
```

#### Custom Check Interval
```bash  
# Check every 2 minutes instead of 5
CHECK_INTERVAL_MINUTES=2
```

#### Timezone Configuration
```bash
# Set timezone for notifications
TZ=America/New_York    # Eastern Time
TZ=Europe/London       # GMT/BST  
TZ=Asia/Tokyo          # JST
TZ=UTC                 # UTC time
```

## 🛠️ Troubleshooting

### Bot Not Responding

#### Check Bot Token
```bash
# Test token validity
curl "https://api.telegram.org/bot1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ/getMe"

# Should return bot information
{
  "ok": true,
  "result": {
    "id": 1234567890,
    "is_bot": true,
    "first_name": "SafeHarbor VPN Monitor",
    "username": "safeharbor_vpn_bot"
  }
}
```

#### Verify Chat ID
```bash
# Check recent messages
curl "https://api.telegram.org/bot1234567890:ABCdefGHIjklMNOpqrsTUVwxyZ/getUpdates"

# Look for your chat ID in the response
```

#### Check Server Logs
```bash
# View Telegram-related logs
docker compose logs keepalive-server | grep -i telegram

# Expected patterns:
# ✅ Telegram message sent successfully
# 📥 Telegram command received: /ping
# Telegram bot polling started
```

### Common Issues

#### "Bot not found" Error
- Verify bot token is correct
- Ensure bot was created successfully via @BotFather
- Check for extra spaces or characters in token

#### "Chat not found" Error  
- Verify chat ID is correct
- Send a message to the bot first (to start the chat)
- Use positive number for chat ID (not negative)

#### Messages Not Received
```bash
# Test direct message sending
curl -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" \
     -d "chat_id=$TELEGRAM_CHAT_ID&text=Test message"

# Check for network connectivity
docker exec keepalive-server curl -s https://api.telegram.org
```

#### Bot Commands Not Working
```bash
# Restart bot polling
docker compose restart keepalive-server

# Check if bot is polling
docker compose logs keepalive-server --tail 20
```

### Log Analysis

#### Successful Bot Operation
```log
[2025-10-12 15:30:26 CEST] 📥 Telegram command received: /status
[2025-10-12 15:30:26 CEST] ✅ Telegram message sent successfully  
[2025-10-12 15:30:26 CEST] ✅ Status response sent
```

#### Bot Errors
```log
[2025-10-12 15:30:26 CEST] ❌ Telegram polling error: HTTPSConnectionPool
[2025-10-12 15:30:26 CEST] ❌ Failed to send Telegram message: Bad Request
```

## 🔐 Security Considerations

### Bot Token Security
- **Never share** your bot token publicly
- **Use environment variables** instead of hardcoding
- **Regenerate token** if compromised via @BotFather

### Chat ID Privacy  
- Chat ID is not sensitive but keep it private
- Only your specific chat will receive notifications
- Bot ignores messages from other users

### Network Security
- Bot communicates over HTTPS with Telegram API
- No sensitive data transmitted (only status information)
- API key protects keepalive endpoints

## 📱 Mobile Usage

### Telegram App Features
- **Push notifications** for instant VPN alerts
- **Reply from notification** to send bot commands  
- **Group chat support** (add bot to group for team monitoring)
- **Message history** to track VPN status over time

### Quick Actions
Set up **Telegram shortcuts** for common commands:
1. Open chat with bot
2. Type `/` to see command suggestions
3. Tap command for instant execution

## 🚀 Advanced Usage

### Multiple Clients
The bot can monitor multiple VPN clients simultaneously:
```
📊 VPN Status

🟢 client-1 - Online
   IP: 185.170.104.53
   Last seen: 1 minute ago

🟢 client-2 - Online  
   IP: 91.236.55.128
   Last seen: 2 minutes ago

🔴 client-3 - Offline
   Last seen: 8 minutes ago
```

### Custom Notifications
Modify `keepalive-server.py` to add custom notification types:
- Bandwidth usage alerts
- Download completion notifications
- Service restart alerts
- System resource warnings

---

**Next:** Learn about [[VPN Monitoring]] features and DNS leak detection.
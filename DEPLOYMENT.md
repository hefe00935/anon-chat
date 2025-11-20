# Deployment & Production Guide

Complete guide to deploy the anonymous communication app to production.

## Architecture Overview

```
┌─────────────────────┐
│   Windows Desktop   │
│   (Electron App)    │
│  - React UI         │
│  - WebRTC Client    │
│  - Private Storage  │
└──────────┬──────────┘
           │ WSS/HTTPS
           ▼
┌─────────────────────┐
│  Signaling Server   │
│  (Node.js + Socket) │
│  - Room Management  │
│  - SDP/ICE Relay    │
│  - Ephemeral Data   │
└──────────┬──────────┘
           │
           ▼
    (Direct P2P)
┌─────────────────────┐
│   TURN Server       │
│   (Coturn)          │
│  - NAT Traversal    │
│  - Media Relay      │
│  - Load Balancing   │
└─────────────────────┘
```

---

## Phase 1: Pre-Production Checklist

### Code Review
- [ ] All crypto functions use secure random (crypto.getRandomValues)
- [ ] No hardcoded secrets or credentials
- [ ] No console.log() sensitive data in production builds
- [ ] Content security policy enforced
- [ ] CORS limited to known origins

### Security Audit
- [ ] All dependencies are from reputable sources
- [ ] Run `npm audit` and resolve any HIGH/CRITICAL issues
- [ ] setContentProtection enabled
- [ ] DevTools disabled
- [ ] Preload script uses contextBridge

### Privacy Verification
- [ ] No message persistence in code
- [ ] Server deletes room data on disconnect
- [ ] Reports deleted after 7 days
- [ ] No user identification
- [ ] Logs sanitized

### Testing
- [ ] All manual tests from QA_TESTING.md passed
- [ ] WebRTC works on test network
- [ ] STUN/TURN configuration validated
- [ ] Load test: 100+ concurrent rooms
- [ ] Stress test: 1+ hour call stability

---

## Phase 2: Infrastructure Setup

### 2.1 Domain & DNS

```bash
# 1. Register domain (e.g., anon-comm.com)
# 2. Point to your VPS IP
# 3. Verify DNS propagation:
nslookup your-domain.com
# Should resolve to your VPS IP
```

### 2.2 VPS Setup (Ubuntu 22.04 LTS)

```bash
# 1. Connect to VPS via SSH
ssh root@your-vps-ip

# 2. Update system
sudo apt-get update && sudo apt-get upgrade -y

# 3. Install Node.js 18
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs npm

# 4. Install PM2 (process manager)
sudo npm install -g pm2

# 5. Install Nginx (reverse proxy)
sudo apt-get install -y nginx

# 6. Install Certbot (SSL certificates)
sudo apt-get install -y certbot python3-certbot-nginx

# 7. Install Git
sudo apt-get install -y git

# 8. Create app user
sudo useradd -m -s /bin/bash appuser
```

### 2.3 Clone & Deploy App

```bash
# As root, create app directory
cd /opt
sudo mkdir anonymous-communication
sudo chown appuser:appuser anonymous-communication

# As appuser
sudo su - appuser
cd /opt/anonymous-communication

# Clone repository
git clone https://github.com/your-username/anonymous-communaction.git .
cd server
npm install --production

# Create .env file
cat > .env << EOF
NODE_ENV=production
PORT=5000
CLIENT_URL=https://your-domain.com
EOF

# Exit to root
exit
```

### 2.4 SSL Certificates

```bash
# Obtain Let's Encrypt certificate
sudo certbot certonly --standalone \
  -d your-domain.com \
  -d www.your-domain.com \
  --agree-tos \
  --email admin@your-domain.com

# Certificates are in:
# /etc/letsencrypt/live/your-domain.com/

# Setup auto-renewal
sudo systemctl enable certbot.timer
sudo systemctl start certbot.timer

# Test renewal
sudo certbot renew --dry-run
```

### 2.5 Nginx Configuration

```bash
# Edit Nginx config
sudo nano /etc/nginx/sites-available/default

# Replace content with:
```

```nginx
# HTTP redirect to HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name your-domain.com www.your-domain.com;
    return 301 https://$server_name$request_uri;
}

# HTTPS server
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name your-domain.com www.your-domain.com;

    # SSL certificates
    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

    # Security headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # Gzip compression
    gzip on;
    gzip_types text/plain application/json;
    gzip_min_length 1000;

    # Proxy to Node.js
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_read_timeout 3600s;
        proxy_send_timeout 3600s;
    }

    # Static files (if needed)
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        proxy_pass http://localhost:5000;
        proxy_cache_valid 200 30d;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
}
```

```bash
# Test Nginx config
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx
sudo systemctl enable nginx

# Check status
sudo systemctl status nginx
```

---

## Phase 3: Start Services

### 3.1 Start Node.js Signaling Server

```bash
# As appuser
sudo su - appuser
cd /opt/anonymous-communication/server

# Start with PM2
pm2 start server.js --name "anon-comm-signaling" \
  --log /var/log/anon-comm/server.log \
  --error /var/log/anon-comm/server-error.log

# Make it restart on reboot
pm2 startup
pm2 save

# Check status
pm2 logs anon-comm-signaling
pm2 status
```

### 3.2 Setup TURN Server (Optional but Recommended)

```bash
# Install Coturn
sudo apt-get install -y coturn

# Create config
sudo nano /etc/coturn/turnserver.conf

# Add these settings:
```

```
# Basic settings
listening-ip=0.0.0.0
listening-port=3478
alt-listening-port=3479
external-ip=YOUR.PUBLIC.IP.HERE
relay-ip=YOUR.PUBLIC.IP.HERE

# TLS settings
cert=/etc/letsencrypt/live/your-domain.com/fullchain.pem
pkey=/etc/letsencrypt/live/your-domain.com/privkey.pem

# Realm and auth
realm=your-domain.com
server-name=your-domain.com
user=anon_user_1:anon_password_1
user=anon_user_2:anon_password_2

# Performance
bps-capacity=0
max-bps-capacity=1000000
total-quota=100000
user-quota=10000
stale-nonce=600

# Security
fingerprint
no-mcast
no-cli
no-loopback-peers

# Logging
log-file=/var/log/coturn/turnserver.log
verbosity=1
```

```bash
# Enable and start Coturn
sudo systemctl enable coturn
sudo systemctl start coturn
sudo systemctl status coturn

# Check listening ports
sudo netstat -tulpn | grep 347
```

### 3.3 Firewall Configuration

```bash
# Allow HTTP/HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow TURN
sudo ufw allow 3478/tcp
sudo ufw allow 3478/udp
sudo ufw allow 3479/tcp
sudo ufw allow 3479/udp

# Allow SSH (important!)
sudo ufw allow 22/tcp

# Enable firewall
sudo ufw enable
sudo ufw status
```

---

## Phase 4: Update Client Configuration

### 4.1 Build Client for Production

```powershell
# On your local development machine
cd client

# Update server URL in App.jsx
# Change: new SignalingClient('http://localhost:5000')
# To: new SignalingClient('wss://your-domain.com')

# Update TURN servers in RTCManager.js
# Add your TURN server credentials

# Build for production
npm run electron-builder

# Output: dist/Anonymous Communication 1.0.0.exe
```

### 4.2 Distribute Installer

```powershell
# Sign the executable (optional but recommended for production)
# Requires code signing certificate

$params = @{
    FilePath = "dist\Anonymous Communication 1.0.0.exe"
    CertPath = "C:\certs\codesign.pfx"
    CertPassword = "your-cert-password"
    TimeStampServer = "http://timestamp.comodoca.com/authenticode"
}

Set-AuthenticodeSignature @params

# Distribute via:
# 1. Direct download link
# 2. GitHub Releases
# 3. Microsoft Store (requires additional setup)
# 4. MSIX installer (Windows app package)
```

---

## Phase 5: Monitoring & Maintenance

### 5.1 Health Checks

```bash
# Monitor server status
curl https://your-domain.com/health

# Expected response:
# {"status":"ok","timestamp":"2024-11-14T12:00:00.000Z","rooms":0,"reports":0}

# Get statistics
curl https://your-domain.com/stats

# Monitor processes
pm2 status
pm2 logs anon-comm-signaling

# Monitor system
top
free -h
df -h
```

### 5.2 Log Management

```bash
# View server logs
tail -f /var/log/anon-comm/server.log

# View error logs
tail -f /var/log/anon-comm/server-error.log

# Archive old logs (weekly)
sudo logrotate -f /etc/logrotate.d/anon-comm

# Create logrotate config
sudo nano /etc/logrotate.d/anon-comm
```

```
/var/log/anon-comm/*.log {
    daily
    rotate 7
    compress
    delaycompress
    notifempty
    create 0640 appuser appuser
    sharedscripts
    postrotate
        pm2 reloadLogs
    endscript
}
```

### 5.3 Auto-Update Mechanism

```bash
# Create update check script
sudo nano /opt/anonymous-communication/check-updates.sh
```

```bash
#!/bin/bash

cd /opt/anonymous-communication
git fetch origin

# Check if there are updates
if [ $(git rev-parse HEAD) != $(git rev-parse origin/main) ]; then
    echo "Updates available, pulling..."
    git pull origin main
    
    # Reinstall dependencies
    npm install --production
    
    # Restart server
    pm2 restart anon-comm-signaling
    
    # Log the update
    echo "$(date): Updated to $(git rev-parse HEAD)" >> /var/log/anon-comm/updates.log
fi
```

```bash
# Make executable
chmod +x /opt/anonymous-communication/check-updates.sh

# Add to crontab (run daily at 2 AM)
sudo crontab -e

# Add this line:
0 2 * * * /opt/anonymous-communication/check-updates.sh >> /var/log/anon-comm/cron.log 2>&1
```

### 5.4 Backup Strategy

```bash
# Create backup directory
sudo mkdir -p /backups/anon-comm
sudo chown appuser:appuser /backups/anon-comm

# Backup script
sudo nano /opt/anonymous-communication/backup.sh
```

```bash
#!/bin/bash

BACKUP_DIR="/backups/anon-comm"
DATE=$(date +%Y-%m-%d_%H-%M-%S)
BACKUP_FILE="$BACKUP_DIR/backup_$DATE.tar.gz"

# Create backup
tar -czf "$BACKUP_FILE" \
    /opt/anonymous-communication \
    /etc/letsencrypt

# Keep only last 30 days
find "$BACKUP_DIR" -type f -mtime +30 -delete

echo "Backup created: $BACKUP_FILE"
```

```bash
# Make executable
chmod +x /opt/anonymous-communication/backup.sh

# Add to crontab (daily at 3 AM)
0 3 * * * /opt/anonymous-communication/backup.sh
```

---

## Phase 6: Troubleshooting Production

### Issue: Server Won't Start

```bash
# Check logs
pm2 logs anon-comm-signaling --err

# Common causes:
# 1. Port already in use
sudo netstat -tulpn | grep 5000

# 2. Module not installed
npm install --production

# 3. .env file missing
cat server/.env

# 4. Permission denied
sudo chown appuser:appuser /opt/anonymous-communication -R
```

### Issue: Nginx Not Proxying

```bash
# Test config
sudo nginx -t

# Check Nginx logs
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/nginx/access.log

# Verify Node.js is running on port 5000
curl http://localhost:5000/health

# Verify firewall allows
sudo ufw show added
```

### Issue: SSL Certificate Issues

```bash
# Check certificate validity
openssl x509 -in /etc/letsencrypt/live/your-domain.com/fullchain.pem -text -noout | grep -A 2 "Validity"

# Renew certificate manually
sudo certbot renew --force-renewal

# Test auto-renewal
sudo certbot renew --dry-run
```

### Issue: High Memory Usage

```bash
# Check memory
pm2 status

# If leaking, restart (quick fix)
pm2 restart anon-comm-signaling

# For permanent fix, debug with:
node --inspect=9229 server.js
# Then use Chrome DevTools (chrome://inspect) to profile

# Or enable memory monitoring
pm2 monitor
```

### Issue: WebRTC Calls Failing

1. **Check STUN**
   ```bash
   # Test STUN server
   npm install -g stunclient
   stunclient stun.l.google.com 19302
   ```

2. **Check TURN** (if configured)
   ```bash
   # Test TURN server
   npm install -g turnutils
   turnutils_uclient -v -n 2 -u user1 -w password your-domain.com
   ```

3. **Check Firewall**
   ```bash
   sudo ufw show added
   # Should include 3478/tcp, 3478/udp, etc.
   ```

4. **Check Client Config**
   - Verify RTCManager.js has TURN servers
   - Check that credentials are valid (not expired)

---

## Performance Optimization

### 5.1 Nginx Optimization

```bash
# Increase worker connections
sudo nano /etc/nginx/nginx.conf

# Find: worker_connections 768;
# Change to: worker_connections 2048;

# Add to http block:
keepalive_timeout 65;
client_max_body_size 10M;
```

### 5.2 Node.js Optimization

```bash
# Use cluster mode (multiple processes)
# Create cluster.js wrapper

# Or with PM2:
pm2 start server.js -i max --name "anon-comm-signaling"
```

### 5.3 Database Considerations

Currently, the app uses **zero persistent storage**. For future scaling:

- **Message Archiving**: Implement optional database for reports only (SQLite, PostgreSQL)
- **Metrics**: Add InfluxDB for real-time stats
- **Caching**: Add Redis for session state (if needed)

---

## Scaling Strategy

### Horizontal Scaling (Multiple Servers)

```
[Load Balancer - Nginx]
        ↓
    [Server 1] ━━━┓
                 [Shared TURN]
    [Server 2] ━━━┛

# Use sticky sessions for Socket.io
# Each room sticks to one server
```

### Vertical Scaling (Bigger Server)

- Upgrade VPS CPU/RAM
- No code changes needed
- Monitor: `top`, `free`, `iostat`

---

## Disaster Recovery

### Failure Scenarios

1. **Server Crash**
   - PM2 auto-restarts
   - All room data auto-deleted (expected)
   - Users must rejoin

2. **Network Partition**
   - Signaling server becomes unreachable
   - Users see "Connection lost" error
   - Auto-reconnect every 5 seconds
   - Retry limit: 5 attempts

3. **Database Corruption**
   - N/A (no persistent DB)
   - Manual cleanup not needed

4. **SSL Certificate Expired**
   - Certbot auto-renews 30 days before expiration
   - If auto-renewal fails:
     ```bash
     sudo certbot renew --force-renewal
     sudo systemctl restart nginx
     ```

---

## Cost Estimation

| Component | Provider | Cost/Month | Notes |
|-----------|----------|-----------|-------|
| VPS (2 CPU, 4GB RAM) | DigitalOcean, Linode | $12-20 | Adequate for 100+ concurrent users |
| Domain | Namecheap, Google | $10-15 | .com domain |
| Bandwidth | Included in VPS | - | 100GB free, then $5-10/100GB |
| SSL Certificate | Let's Encrypt | Free | Auto-renewed |
| **Total** | | **$22-45** | Supports production usage |

---

## Compliance & Legal

### Privacy Policy Requirements

- ✅ State what data is collected (nothing)
- ✅ State retention period (0 days)
- ✅ Explain E2EE (optional, WebRTC is encrypted)
- ✅ Link to this deployment guide
- ✅ List support contact

### GDPR Compliance (If serving EU users)

- ✅ Right to be forgotten (no data stored)
- ✅ Data portability (n/a)
- ✅ Processing agreement (n/a)

### Data Protection Officer (DPO)

Required if:
- Processing large amounts of data (N/A - we don't)
- Systematic monitoring (N/A)

You're exempt with this app!

---

## Monitoring Dashboard

Create a simple status page:

```bash
# Install status dashboard
sudo npm install -g status-page

# Or use: https://github.com/upptime/upptime
```

---

## Checklist Before Going Live

- [ ] Server deployed to VPS
- [ ] SSL certificate configured
- [ ] Nginx reverse proxy working
- [ ] Health check returns 200 OK
- [ ] WebRTC calls work in test
- [ ] PM2 auto-restart configured
- [ ] Logs are being written
- [ ] Firewall allows necessary ports
- [ ] Backups running daily
- [ ] Monitoring alerts configured
- [ ] Privacy policy published
- [ ] Support email configured
- [ ] Client updated with production URLs

---

**Last Updated**: November 2024
**Maintained By**: [Your Organization]
**Support**: [support@your-domain.com]

# Complete Run Instructions - Copy & Paste Ready

This file contains exact commands to set up and run the app locally.

## Environment

Tested on:
- Windows 10/11
- Node.js 18.18.0+
- PowerShell (built-in)

## Commands

### 1. Navigate to project

```powershell
cd c:\Users\Pc\Desktop\anonymous-communaction
```

### 2. Install Server Dependencies

```powershell
cd server
npm install
```

Expected output:
```
added 78 packages in 8.234s
```

### 3. Install Client Dependencies

```powershell
cd ..\client
npm install
```

Expected output:
```
added 327 packages in 45.234s
```

### 4. Start Server (Terminal 1)

```powershell
cd ..\server
npm start
```

Wait for:
```
🚀 Signaling server running on port 5000
📡 WebSocket endpoint: ws://localhost:5000
📊 Health check: http://localhost:5000/health
📈 Statistics: http://localhost:5000/stats

🔐 PRIVACY: Room data is ephemeral and auto-deleted on disconnect
```

### 5. Start Vite Dev Server (Terminal 2)

```powershell
cd ..\client
npm run dev
```

Wait for:
```
  VITE v5.0.0  ready in 234 ms

  ➜  Local:   http://localhost:5173/
  ➜  press h to show help
```

### 6. Start Electron Client (Terminal 3)

```powershell
# Still in client folder
npm run electron-dev
```

Wait for Electron window to open showing the Landing screen.

## Testing the App

### Test 1: Create & Join Session

1. In Electron window:
   - Click "✨ Create New Session"
   - You'll see a code like "ABC12345"
   - Click "Copy Code"

2. File → New Window (or open another Electron instance)
   - Paste code in "Join Session" field
   - Click "Join Session"

3. Both windows now show chat screen
   - Type message in one window
   - See it appear instantly in the other

### Test 2: Voice/Video Call

1. In one chat window:
   - Click "📞 Start Call"
   - Grant microphone/camera permission if prompted
   - See local video (PiP) and wait for remote

2. In the other window:
   - You should see an incoming call indicator
   - Click "📞 Start Call" to accept
   - Video streams appear (local small, remote large)

3. Test controls:
   - 🎤 = Mute/unmute
   - 🎥 = Camera on/off
   - 📊 = Show connection stats
   - ☎️ = End call

### Test 3: Privacy Features

1. **Message Auto-Delete**
   - Send a message
   - Close one client window
   - Reopen and join room
   - Message is gone ✓

2. **PrintScreen Blocking**
   - Press PrintScreen
   - Nothing gets captured ✓

3. **One-Way Code**
   - Create session with code "ABC12345"
   - Close that session
   - Try to join "ABC12345" with another client
   - Should get "Room not found" ✓

## Troubleshooting During Setup

### "Cannot find module" error

**Problem**: Dependencies not installed

**Fix**:
```powershell
# In the folder where error occurred
npm install
# Then try again
```

### "Port 5000 already in use"

**Problem**: Server already running

**Fix**:
```powershell
# Kill the process
Get-Process -Port 5000 | Stop-Process -Force
# Or just use different port:
# Edit server/server.js: const PORT = 5001
```

### Electron window is blank/white

**Problem**: Vite dev server not ready

**Fix**:
1. Make sure Vite is running in terminal 2
2. Wait 30 seconds for build
3. Close Electron and try `npm run electron-dev` again

### "Failed to connect to server"

**Problem**: Server not running or wrong port

**Fix**:
```powershell
# Check if server is running:
curl http://localhost:5000/health
# Output should show: {"status":"ok",...}

# If not, start server:
npm start
```

### No audio/video in call

**Problem**: Browser/OS permissions or hardware issue

**Fix**:
1. Settings → Privacy & Security → Microphone → Electron should be ON
2. Settings → Privacy & Security → Camera → Electron should be ON
3. Try different device in Settings
4. Check Device Manager for hardware

## Building for Production

### Package Electron App (Windows)

```powershell
cd client

# Build and package
npm run electron-builder

# Output appears in: dist/Anonymous Communication 1.0.0.exe
```

### Deploy Server to VPS

```bash
# On your VPS (Ubuntu/Debian):

# 1. Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# 2. Clone repo
cd /opt
sudo git clone https://github.com/your-username/anonymous-communaction.git
cd anonymous-communaction/server

# 3. Install dependencies (production only)
npm install --production

# 4. Update server URL in client:
# Edit client/src/App.jsx line ~30:
# const [signalingClient] = useState(() => 
#   new SignalingClient('wss://your-domain.com')
# );

# 5. Start with PM2
sudo npm install -g pm2
pm2 start server.js --name "anon-comm"
pm2 startup
pm2 save

# 6. Setup HTTPS (certbot)
sudo apt-get install certbot
sudo certbot certonly --standalone -d your-domain.com
# Edit server.js to use HTTPS (see README.md)

# 7. Update port to 443 and restart
```

## Performance Checklist

- [ ] Server running on port 5000
- [ ] Client dev server on port 5173
- [ ] Electron opens without errors
- [ ] Can create session (generates 8-char code)
- [ ] Can join session with code
- [ ] Can send/receive messages
- [ ] Can initiate call and see video
- [ ] Call stats show > 0 bytes sent/received
- [ ] Can end call and return to chat

## Next Steps

1. **Add TURN server** (see infra/COTURN_SETUP.md)
2. **Deploy to VPS** (see "Deploy Server to VPS" above)
3. **Get domain & HTTPS** (use Cloudflare or Let's Encrypt)
4. **Build desktop installer** (use `npm run electron-builder`)
5. **Share executable** (dist/*.exe with friends)

---

**Having issues?** Check the main README.md Troubleshooting section or open a GitHub issue.

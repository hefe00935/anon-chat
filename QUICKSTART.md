# 🚀 QUICK START GUIDE - Run This Now!

## ⏱️ Estimated Time: 10 minutes (setup) + 2 minutes (first run)

---

## Step 1: Open PowerShell (2 minutes)

```powershell
# Open Windows PowerShell or PowerShell 7+
# Click Start → PowerShell

# Navigate to the project
cd c:\Users\Pc\Desktop\anonymous-communaction

# Verify project exists
ls
# You should see: client, server, README.md, etc.
```

---

## Step 2: Install Dependencies (4 minutes)

```powershell
# Install all dependencies (one command)
npm run install-all

# This runs:
# cd server && npm install
# cd ../client && npm install

# Wait for completion... (watch for "added X packages")
```

If you see any warnings about vulnerabilities, that's OK for local dev.

---

## Step 3: Start Server (Terminal 1) (1 minute)

```powershell
# Open a NEW PowerShell window
cd c:\Users\Pc\Desktop\anonymous-communaction\server

npm start

# Wait for:
# 🚀 Signaling server running on port 5000
# 📡 WebSocket endpoint: ws://localhost:5000
# 📊 Health check: http://localhost:5000/health
```

✅ **Server is running.** Keep this window open.

---

## Step 4: Start Vite Dev Server (Terminal 2) (1 minute)

```powershell
# Open ANOTHER NEW PowerShell window
cd c:\Users\Pc\Desktop\anonymous-communaction\client

npm run dev

# Wait for:
# VITE v5.0.0  ready in XXX ms
# ➜  Local:   http://localhost:5173/
```

✅ **Vite is running.** Keep this window open.

---

## Step 5: Start Electron App (Terminal 3) (30 seconds)

```powershell
# Open ANOTHER NEW PowerShell window
cd c:\Users\Pc\Desktop\anonymous-communaction\client

npm run electron-dev

# An Electron window should open showing the app
```

✅ **App is running!** You should see the Landing screen.

---

## Step 6: Test the App (2 minutes)

### Test 1: Create a Room
```
1. Click "✨ Create New Session"
2. You'll see a code like "ABC12345"
3. Write it down or remember it
4. Chat screen appears
```

### Test 2: Join the Room (Second Window)
```
1. File → New Window (or open another Electron instance)
2. Click "Join Session"
3. Paste the code "ABC12345"
4. Click "Join Session"
5. Both windows should connect
```

### Test 3: Send a Message
```
1. In one window, type "Hello"
2. Click "Send"
3. Watch it appear in the other window instantly! ✨
```

### Test 4: Make a Call
```
1. Click "📞 Start Call"
2. Grant camera/mic permissions if asked
3. In the other window, click "📞 Start Call" to accept
4. Video should appear!
5. Click "☎️" to end call
```

---

## 🎉 Success!

You've successfully:
- ✅ Set up the development environment
- ✅ Started the server
- ✅ Started the client
- ✅ Created a room
- ✅ Sent messages
- ✅ Made a video call

---

## 📖 What's Next?

### To Learn More
- **README.md** - Complete guide with all features
- **PROJECT_SUMMARY.md** - Architecture & design
- **SECURITY.md** - Privacy & protection details
- **DEPLOYMENT.md** - How to deploy to production

### To Make Changes
Edit files in `client/src/` and watch them reload automatically (Vite HMR).

### To Test on Another Device
1. Find your machine's IP: `ipconfig` → IPv4
2. On other device, update `client/src/App.jsx`:
   ```javascript
   new SignalingClient('http://YOUR-IP:5000')
   ```
3. Run on other device the same way

### To Build for Distribution
```powershell
cd client
npm run electron-builder
# Creates: dist/Anonymous Communication 1.0.0.exe
```

---

## 🐛 Troubleshooting (First 5 Issues)

### Issue 1: "Failed to connect to server"
```powershell
# Make sure server is running in Terminal 1
# See "🚀 Signaling server running on port 5000"
# If not running, go back to Step 3
```

### Issue 2: "Port 5000 already in use"
```powershell
# Kill the old process:
Get-Process -Port 5000 | Stop-Process -Force
# Then try: npm start again
```

### Issue 3: "Cannot find module" error
```powershell
# In the folder where error occurred (client or server):
npm install
# Then try again
```

### Issue 4: Electron window is blank/white
```powershell
# Wait 30 seconds for Vite to build
# Or in client terminal, look for "ready in XXX ms"
# Then close and reopen Electron window
```

### Issue 5: No video in call
```powershell
# Check browser permissions:
# Settings → Privacy & Security → Camera → Turn ON
# Settings → Privacy & Security → Microphone → Turn ON
# Then restart the app
```

---

## 🔗 Quick Links

| Document | Purpose |
|----------|---------|
| **README.md** | Full feature guide |
| **RUN_NOW.md** | Detailed setup steps |
| **SECURITY.md** | Privacy protections |
| **QA_TESTING.md** | Test cases |
| **DEPLOYMENT.md** | Production setup |
| **FUTURE.md** | What's coming |

---

## 💡 Key Features

- 🔐 **Anonymous**: No accounts, just codes
- 💬 **Messages**: Real-time chat, auto-delete
- 📞 **Calling**: 1:1 video + voice via WebRTC
- 🛡️ **Private**: Screen capture protection, no logs
- 🚀 **Fast**: < 50ms latency on local network

---

## 📞 Need Help?

1. Check **RUN_NOW.md** for detailed commands
2. Check **README.md** Troubleshooting section
3. Check **QA_TESTING.md** for test procedures
4. Check **SECURITY.md** for privacy questions
5. Read **PROJECT_SUMMARY.md** for architecture

---

## 📝 System Requirements Met?

Before proceeding, verify:

```powershell
# Check Node.js version (need 18+)
node --version
# Should show: v18.0.0 or higher

# Check npm version (need 6+)
npm --version
# Should show: 6.0.0 or higher

# Check Windows (version 10 or 11)
# Run: winver
```

---

## 🎓 Learning Path

1. **Get it running** ← You are here
2. **Read README.md** - Understand all features
3. **Study App.jsx** - See how state flows
4. **Review RTCManager.js** - Learn WebRTC
5. **Check server.js** - Understand signaling
6. **Read SECURITY.md** - Know privacy details
7. **Explore FUTURE.md** - Plan improvements

---

## 🔄 Development Loop

After setup, your workflow is:

```powershell
# Terminal 1: Keep running
cd server && npm start

# Terminal 2: Keep running
cd client && npm run dev

# Terminal 3: Keep running
cd client && npm run electron-dev

# When you edit code:
# - React files: Auto-reload (Vite HMR)
# - Server files: Restart with npm run dev
# - Electron: May need window reload (Ctrl+R)
```

---

## ✅ Daily Checklist

Before you start working:

- [ ] 3 PowerShell windows open
- [ ] Server running (`npm start`)
- [ ] Vite running (`npm run dev`)
- [ ] Electron running (`npm run electron-dev`)
- [ ] Check http://localhost:5000/health returns {"status":"ok",...}
- [ ] Test: Create room, join, send message

---

## 🎯 Common Tasks

### Add a new component
```javascript
// client/src/components/NewComponent.jsx
export default function NewComponent() {
  return <div>New component</div>;
}

// In App.jsx or parent:
import NewComponent from './components/NewComponent';
```

### Modify styling
```css
/* client/src/index.css or use Tailwind classes */
/* Vite will hot-reload immediately */
```

### Change server behavior
```javascript
// server/server.js
// Edit socket event handlers
// Restart: Ctrl+C in Terminal 1, then npm start
```

### Add environment variable
```
// server/.env
MY_VAR=my_value

// In server.js:
process.env.MY_VAR
```

---

## 📊 Expected Console Output

### Server Console (Terminal 1)
```
🚀 Signaling server running on port 5000
📡 WebSocket endpoint: ws://localhost:5000
📊 Health check: http://localhost:5000/health
📈 Statistics: http://localhost:5000/stats

🔐 PRIVACY: Room data is ephemeral and auto-deleted on disconnect
```

### Vite Console (Terminal 2)
```
VITE v5.0.0  ready in 234 ms

➜  Local:   http://localhost:5173/
➜  press h to show help
```

### Electron Console (Terminal 3)
```
(Just the Electron window opens)
```

---

## 🔒 Privacy Verified

After running:
- [ ] Try PrintScreen - doesn't capture app
- [ ] Press F12 - doesn't open DevTools
- [ ] Send message, disconnect - message gone
- [ ] Create room code "ABC12345" - single use only

---

## 🚢 Next Phase: Production

Once you're comfortable with development:

1. Read **DEPLOYMENT.md** - Deploy to VPS
2. Read **COTURN_SETUP.md** - Set up TURN server
3. Follow **DEPLOYMENT.md** - SSL, Nginx, PM2
4. Build Windows installer - `npm run electron-builder`
5. Share the .exe with friends or users

---

**🎉 Congratulations! You're now running the anonymous communication app locally.**

**Questions?** Check the main README.md or other .md files.

**Ready to deploy?** Check DEPLOYMENT.md next.

**Want to contribute?** See FUTURE.md for ideas.

---

**Last Updated**: November 2024  
**Time to Get Running**: ~15 minutes  
**Difficulty**: Easy (follow steps exactly)

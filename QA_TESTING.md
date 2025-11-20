# QA & Testing Guide

Complete testing checklist for the anonymous communication app.

## Pre-Test Setup

### Requirements
- Windows 10/11
- Node.js 18+
- 2+ test devices (or 2 windows on same device)
- Microphone and camera
- Internet connection

### Setup Steps

1. **Start server**
   ```powershell
   cd server
   npm start
   ```

2. **Start Vite dev server**
   ```powershell
   cd client
   npm run dev
   ```

3. **Start Electron app** (in separate terminal)
   ```powershell
   npm run electron-dev
   ```

4. **Health check**
   ```powershell
   curl http://localhost:5000/health
   # Should return: {"status":"ok",...}
   ```

---

## Functional Tests

### Test Category: Room Management

#### Test 1.1: Create Room
- **Steps**:
  1. Click "✨ Create New Session"
  2. Observe room code (8 characters, uppercase)
  3. Note the ephemeral ID shown
- **Expected Result**:
  - Room code is exactly 8 alphanumeric chars
  - Code appears in header: "Code: ABC12345"
  - Green "live" indicator shows
  - Chat screen displays with empty message history
- **Status**: ✅ Pass / ❌ Fail

#### Test 1.2: Join Room with Code
- **Steps**:
  1. Window 1: Create room, get code "ABC12345"
  2. Window 2: Join Session, paste "ABC12345"
  3. Click "Join Session"
- **Expected Result**:
  - Window 2 joins immediately
  - Both show same code in header
  - Both can see each other's messages
  - No error dialog
- **Status**: ✅ Pass / ❌ Fail

#### Test 1.3: Invalid Code Format
- **Steps**:
  1. Try joining with "ABC1234" (only 7 chars)
  2. Try joining with "abc12345" (lowercase)
  3. Try joining with "ABC12345!" (special char)
- **Expected Result**:
  - Error message: "Invalid room code format"
  - No connection attempted
  - Can retry with valid code
- **Status**: ✅ Pass / ❌ Fail

#### Test 1.4: Non-Existent Room
- **Steps**:
  1. Try joining with "ZZZZZZZZ" (never created)
- **Expected Result**:
  - Error: "Room not found"
  - Not stuck in loading state
  - Can try another code
- **Status**: ✅ Pass / ❌ Fail

#### Test 1.5: Room Capacity (Max 2 People)
- **Steps**:
  1. Window 1: Create room
  2. Window 2: Join room
  3. Window 3: Try joining same room
- **Expected Result**:
  - Window 3 gets error: "Room is full"
  - Windows 1 & 2 unaffected
  - Window 3 can create new room or wait
- **Status**: ✅ Pass / ❌ Fail

#### Test 1.6: Leave Room
- **Steps**:
  1. Join room with 2 windows
  2. Window 1: Click "Leave"
  3. Window 2: Observe
- **Expected Result**:
  - Window 1 returns to Landing screen
  - Window 2 shows "participant-left" (internal log)
  - Window 2 can still send messages
  - Room is deleted from server (auto-cleanup)
- **Status**: ✅ Pass / ❌ Fail

---

### Test Category: Messaging

#### Test 2.1: Send & Receive Text
- **Steps**:
  1. Join room with 2 windows
  2. Window 1: Type "Hello" and click "Send"
  3. Window 2: Observe
- **Expected Result**:
  - Message appears in both windows within 100ms
  - Message shows in blue bubble (sender) and gray (receiver)
  - Timestamp is correct
  - Message animates in (slide + fade)
- **Status**: ✅ Pass / ❌ Fail

#### Test 2.2: Message Auto-Delete
- **Steps**:
  1. Send messages in room
  2. Disconnect (close one window)
  3. Reconnect with same or new window
- **Expected Result**:
  - Old messages are NOT visible
  - Only new messages sent after reconnect appear
  - No message persistence in server logs
- **Status**: ✅ Pass / ❌ Fail

#### Test 2.3: Empty Message Prevention
- **Steps**:
  1. Leave message input blank
  2. Click "Send"
- **Expected Result**:
  - Button disabled (opacity 50%)
  - No empty message sent
  - No error dialog
- **Status**: ✅ Pass / ❌ Fail

#### Test 2.4: Long Message
- **Steps**:
  1. Type message > 1000 characters
  2. Send
- **Expected Result**:
  - Message sends and wraps correctly
  - No truncation
  - Layout doesn't break
  - Scroll works if many messages
- **Status**: ✅ Pass / ❌ Fail

#### Test 2.5: Message with Special Characters
- **Steps**:
  1. Send: "Hello 👋 你好 \n @everyone #test $$$"
  2. Observe in receiver
- **Expected Result**:
  - Emojis render correctly
  - Unicode characters display
  - Newlines preserved (if supported)
  - No corruption
- **Status**: ✅ Pass / ❌ Fail

#### Test 2.6: Message Timestamps
- **Steps**:
  1. Send message
  2. Hover over timestamp
- **Expected Result**:
  - Timestamp shows in HH:MM format (12 or 24 hour)
  - Timestamp is local time (device timezone)
  - Timestamp is accurate (within 5 seconds)
- **Status**: ✅ Pass / ❌ Fail

---

### Test Category: WebRTC Calls

#### Test 3.1: Initiate Call
- **Steps**:
  1. Join room with 2 windows (both have cameras)
  2. Window 1: Click "📞 Start Call"
  3. Grant microphone/camera permissions if prompted
- **Expected Result**:
  - Window 1 switches to Call Screen
  - Local video appears in bottom-right (small)
  - Small loading indicator or "waiting for peer..."
  - No error
  - Timeout after 30 seconds if no peer
- **Status**: ✅ Pass / ❌ Fail

#### Test 3.2: Accept Call
- **Steps**:
  1. Window 1 initiates call (see 3.1)
  2. Window 2: Observe for incoming call indicator
  3. Click "📞 Start Call" to accept
- **Expected Result**:
  - Window 2 switches to Call Screen
  - Both show local video (small) and remote video (large)
  - Video quality is good (not pixelated)
  - Audio is clear (test by speaking)
  - Latency < 500ms
- **Status**: ✅ Pass / ❌ Fail

#### Test 3.3: Audio Mute Toggle
- **Steps**:
  1. In call, click "🎤" button
  2. Speak in Window 1
  3. Listen in Window 2
- **Expected Result**:
  - Button color changes (green → red when muted)
  - Muted user's mic doesn't transmit
  - Button text/tooltip says "Mute" / "Unmute"
  - Can toggle on/off repeatedly
- **Status**: ✅ Pass / ❌ Fail

#### Test 3.4: Video Toggle
- **Steps**:
  1. In call, click "🎥" button
  2. Observe local video
  3. Observe remote side's view of you
- **Expected Result**:
  - Local video disappears and shows 📹 placeholder
  - Remote side sees black screen / placeholder
  - Button color changes (green → red when off)
  - Can toggle repeatedly
  - Video resumes when re-enabled
- **Status**: ✅ Pass / ❌ Fail

#### Test 3.5: Call Stats
- **Steps**:
  1. In call, click "📊" button
  2. Observe stats panel
- **Expected Result**:
  - Stats panel appears below controls
  - Shows audio in/out (bytes, jitter)
  - Shows video in/out (bytes, FPS)
  - Stats update every second
  - Click 📊 again to hide
- **Status**: ✅ Pass / ❌ Fail

#### Test 3.6: End Call
- **Steps**:
  1. In call, click "☎️" (red) button
- **Expected Result**:
  - Call ends immediately
  - Returned to chat screen
  - Local video stream stopped (cleaned up)
  - Remote stream stopped
  - Message history is preserved
  - Can start new call if desired
- **Status**: ✅ Pass / ❌ Fail

#### Test 3.7: Call Timeout (No Answer)
- **Steps**:
  1. Window 1: Start call
  2. Window 2: Do NOT join
  3. Wait 30+ seconds
- **Expected Result**:
  - Window 1 times out or shows error after 30s
  - Can try calling again or go back to chat
  - No stuck state
- **Status**: ✅ Pass / ❌ Fail

#### Test 3.8: Connection Loss During Call
- **Steps**:
  1. In call, disconnect internet (or use network throttle)
  2. Observe after 5-10 seconds
- **Expected Result**:
  - Video freezes (expected)
  - Or call drops with error message
  - Can return to chat
  - Can end call manually
- **Status**: ✅ Pass / ❌ Fail

---

### Test Category: Privacy & Security

#### Test 4.1: PrintScreen Blocking
- **Steps**:
  1. App window visible with message or video
  2. Press "PrintScreen" key
  3. Open Paint, paste
- **Expected Result**:
  - Nothing pasted
  - Clipboard empty
  - No screenshot captured
- **Status**: ✅ Pass / ❌ Fail

#### Test 4.2: F12 (DevTools) Blocking
- **Steps**:
  1. Press "F12"
  2. Try "Ctrl+Shift+I"
  3. Try "Ctrl+Shift+J"
- **Expected Result**:
  - No developer tools appear
  - No error in console (if tools were already open)
  - Nothing happens (silent block)
- **Status**: ✅ Pass / ❌ Fail

#### Test 4.3: F11 (Fullscreen) Blocking
- **Steps**:
  1. Press "F11"
- **Expected Result**:
  - No fullscreen toggle
  - Window stays windowed
- **Status**: ✅ Pass / ❌ Fail

#### Test 4.4: Context Menu Disabled
- **Steps**:
  1. Right-click anywhere in app
- **Expected Result**:
  - No context menu appears
  - No "Inspect Element", "Save Image", etc.
- **Status**: ✅ Pass / ❌ Fail

#### Test 4.5: OBS/Screen Recording Protection
- **Steps**:
  1. Open OBS Studio
  2. Add "Window Capture" source
  3. Select app window
  4. Try to record
- **Expected Result**:
  - Window appears black or protected (depending on OS)
  - Or with setContentProtection, may show black areas
  - Recording doesn't capture meaningful content
- **Status**: ✅ Pass / ❌ Fail
- **Note**: Results vary by OS and tool; protection is "best effort"

#### Test 4.6: Report Function
- **Steps**:
  1. In chat, click "🚩" button
  2. Type reason: "Abusive language"
  3. Click "Submit Report"
- **Expected Result**:
  - Modal closes
  - Toast: "Report submitted successfully"
  - Server logs show report received (dev mode)
  - No identifying info in report
- **Status**: ✅ Pass / ❌ Fail

#### Test 4.7: Ephemeral ID in Landing
- **Steps**:
  1. Open app
  2. Look at "Your Ephemeral ID" section
- **Expected Result**:
  - ID shown: starts with "user_"
  - Shows "Deleted when you close this app"
  - ID is unique per app instance
- **Status**: ✅ Pass / ❌ Fail

---

## Performance Tests

### Test Category: Performance

#### Test 5.1: App Startup Time
- **Steps**:
  1. Close app completely
  2. Measure time from clicking .exe to window visible
- **Expected Result**:
  - < 3 seconds (cold start)
  - < 1 second (warm start)
- **Benchmark**: Latest run: ___ms

#### Test 5.2: Room Creation Time
- **Steps**:
  1. From landing, measure click to chat screen visible
- **Expected Result**:
  - < 500ms
- **Benchmark**: Latest run: ___ms

#### Test 5.3: Message Send Latency
- **Steps**:
  1. Send message, measure to receive
  2. Do 10 messages, average
- **Expected Result**:
  - < 100ms (local network)
  - < 500ms (remote server)
- **Benchmark**: Latest run: ___ms

#### Test 5.4: Video Call Setup Time
- **Steps**:
  1. Click "📞 Start Call"
  2. Measure to "Connected" (both sides receiving video)
- **Expected Result**:
  - < 5 seconds
- **Benchmark**: Latest run: ___s

#### Test 5.5: Video Bitrate
- **Steps**:
  1. In call, click "📊"
  2. Note bytes/sec rate
  3. Estimate bitrate: (bytes/sec × 8) / 1,000,000
- **Expected Result**:
  - 500 kbps - 2.5 Mbps (adaptive)
- **Benchmark**: Latest run: ___Mbps

#### Test 5.6: Memory Usage
- **Steps**:
  1. Task Manager (Ctrl+Shift+Esc)
  2. Right-click column header → Select column → Memory
  3. Find "Electron" process
  4. Record before/after call
- **Expected Result**:
  - Baseline: 150-250 MB
  - During call: 300-400 MB
  - After call: back to baseline
- **Baseline**: ___MB
- **During call**: ___MB
- **Status**: ✅ Pass / ❌ Fail

---

## Compatibility Tests

### Test Category: Devices & Browsers

#### Test 6.1: Different Cameras
- **Steps**:
  1. Test with USB camera
  2. Test with integrated webcam
  3. Test with virtual camera (OBS)
- **Expected Result**:
  - All enumerate correctly
  - Can switch between in call
  - Video streams properly
- **Status**: ✅ Pass / ❌ Fail

#### Test 6.2: Different Microphones
- **Steps**:
  1. Test with Bluetooth headset mic
  2. Test with USB mic
  3. Test with default mic
- **Expected Result**:
  - All work
  - Can switch between
  - Audio clear
- **Status**: ✅ Pass / ❌ Fail

#### Test 6.3: Multiple Displays
- **Steps**:
  1. Drag app window to secondary monitor
  2. Make call
- **Expected Result**:
  - Works on secondary display
  - No rendering issues
  - Video/audio continues
- **Status**: ✅ Pass / ❌ Fail

#### Test 6.4: High DPI (4K Display)
- **Steps**:
  1. On 4K monitor, run app
  2. Check UI scaling
- **Expected Result**:
  - UI elements sharp (not blurry)
  - Text readable
  - No overlap or cutoff
- **Status**: ✅ Pass / ❌ Fail

---

## Network Tests

### Test Category: Network Conditions

#### Test 7.1: LAN (Same Network)
- **Steps**:
  1. Both test devices on same WiFi
  2. Perform messaging + call tests
- **Expected Result**:
  - Latency < 50ms
  - Crystal clear video/audio
  - No packet loss visible
- **Status**: ✅ Pass / ❌ Fail

#### Test 7.2: WAN (Different Networks)
- **Steps**:
  1. Device A: Office WiFi
  2. Device B: Home WiFi
  3. Perform messaging + call tests
- **Expected Result**:
  - Works (depends on NAT type)
  - Latency 50-200ms
  - May need TURN server for video
- **Status**: ✅ Pass / ❌ Fail
- **Note**: If fails, user needs TURN server

#### Test 7.3: Slow Network (Throttled)
- **Steps**:
  1. Use TMeter or NetLimiter to limit to 2 Mbps
  2. Make call
- **Expected Result**:
  - Video still works (lower quality)
  - Audio may buffer slightly
  - No app crash
- **Status**: ✅ Pass / ❌ Fail

#### Test 7.4: High Latency (250ms+)
- **Steps**:
  1. Simulate 250ms latency with TMeter
  2. Have conversation
- **Expected Result**:
  - Users notice delay
  - But communication still works
  - No dropped calls
- **Status**: ✅ Pass / ❌ Fail

#### Test 7.5: Packet Loss (5%)
- **Steps**:
  1. Simulate 5% packet loss
  2. Make call
- **Expected Result**:
  - Video freezes briefly, recovers
  - Audio may be choppy but plays
  - No permanent disconnect
- **Status**: ✅ Pass / ❌ Fail

---

## Stress Tests

### Test Category: Load & Stability

#### Test 8.1: Long Call Duration
- **Steps**:
  1. Start call
  2. Leave running for 1+ hour
  3. Check memory, CPU
- **Expected Result**:
  - No memory leak
  - CPU usage stable
  - Video/audio doesn't degrade
  - Can end call normally
- **Duration**: ___minutes
- **Memory leak**: ✅ No / ❌ Yes
- **Status**: ✅ Pass / ❌ Fail

#### Test 8.2: Many Messages
- **Steps**:
  1. Send 100+ messages rapidly
  2. Measure performance
- **Expected Result**:
  - All messages appear (not dropped)
  - Scroll performance OK
  - No app lag
  - Memory < 500 MB
- **Stress**: 100 messages in 10 seconds
- **Status**: ✅ Pass / ❌ Fail

#### Test 8.3: Room Cycling
- **Steps**:
  1. Create room → Join → Leave → Repeat 10 times
- **Expected Result**:
  - Each cycle takes consistent time
  - No memory accumulation
  - No connection leaks
- **Status**: ✅ Pass / ❌ Fail

#### Test 8.4: App Restart
- **Steps**:
  1. Start call
  2. Close app (force close, Alt+F4)
  3. Reopen
  4. Try creating new room
- **Expected Result**:
  - Clean shutdown
  - No crash dumps
  - Reopens normally
  - New room works
- **Status**: ✅ Pass / ❌ Fail

---

## Accessibility Tests

### Test Category: Accessibility

#### Test 9.1: Keyboard Navigation
- **Steps**:
  1. Disable mouse
  2. Use only Tab and Enter to navigate app
- **Expected Result**:
  - Can navigate all screens
  - Can send message (Tab to input, type, Enter)
  - Can accept call (Tab to button, Enter)
- **Status**: ✅ Pass / ❌ Fail

#### Test 9.2: High Contrast
- **Steps**:
  1. Windows Settings → Contrast themes
  2. Select "High Contrast" theme
  3. Run app
- **Expected Result**:
  - UI readable in high contrast
  - No text disappears
  - Buttons clearly distinguished
- **Status**: ✅ Pass / ❌ Fail

#### Test 9.3: Large Text
- **Steps**:
  1. Windows Settings → Text size → 150%
  2. Reopen app
- **Expected Result**:
  - Text is larger
  - No layout breakage
  - All buttons clickable
- **Status**: ✅ Pass / ❌ Fail

---

## Regression Test Suite

Run this checklist before every release:

- [ ] Create room works
- [ ] Join room works
- [ ] Send message works
- [ ] Message appears on receiver side
- [ ] Initiate call works
- [ ] Accept call works
- [ ] Audio transmits both ways
- [ ] Video transmits both ways
- [ ] Mute button works
- [ ] Video toggle works
- [ ] End call works
- [ ] Return to chat after call
- [ ] Leave room works
- [ ] PrintScreen blocked
- [ ] F12 blocked
- [ ] Report button works
- [ ] Server logs show no sensitive info
- [ ] Memory doesn't leak (< 50MB growth per 10 min)
- [ ] No console errors in production build

---

## Test Results Template

```
Test Run #: ___
Date: ___
Tester: ___
Environment: Windows 10/11, Node 18.x, Electron 27.x
Network: LAN / WAN / Throttled

Functional Tests: ___/✅ Passed
Performance Tests: ___/✅ Passed
Security Tests: ___/✅ Passed
Regression Tests: ___/✅ Passed

Issues Found:
1. [Description]
   - Severity: High/Medium/Low
   - Steps to reproduce: [...]
   - Expected: [...]
   - Actual: [...]
   - Status: New / Investigating / Fixed

Notes:
[Anything else]
```

---

**Last Updated**: November 2024

# Security & Privacy Features

## 🔐 Overview

This application is built with privacy and security as core principles:
- ✅ All data is ephemeral (temporary)
- ✅ End-to-end encrypted audio streams
- ✅ No central storage of messages or calls
- ✅ Screenshot and screen capture blocking
- ✅ Optional message encryption
- ✅ Secure file sharing via P2P

---

## What This App Protects Against

### ✅ Implemented Protections

1. **No Account Required**
   - Zero personally identifiable information collected
   - Anonymous ephemeral IDs generated locally
   - No email, phone, or login needed

2. **Screen Capture Protection (NEW)**
   - Detects PrintScreen, Shift+S (Windows), Cmd+Shift+4 (Mac)
   - Detects F12, Ctrl+Shift+I (Developer Tools)
   - Screen blackout notification: "🔒 Screenshots disabled"
   - Watermark: "PRIVATE - NO SCREENSHOTS"
   - Location: `client/src/utils/screenshotBlocker.js`

3. **Voice Call Security**
   - Audio-only calls (no video exposure)
   - WebRTC SRTP encryption (automatic)
   - Audio streams P2P (server never sees audio)
   - No MediaRecorder API used
   - Microphone permission required before call

4. **Message Privacy**
   - Messages auto-delete after **5 minutes**
   - No persistent server storage
   - In-memory only during session
   - Optional E2E encryption available
   - Location: `client/src/components/Chat.jsx`

5. **Room Password Protection (NEW)**
   - Creators can set optional password
   - Prevents unauthorized access
   - Password hashed before transmission
   - Location: `client/src/components/RoomPassword.jsx`

6. **Secure File Sharing (NEW)**
   - Files transferred via WebRTC DataChannel
   - Never touches server
   - 100MB size limit
   - Auto-deleted after transfer
   - P2P encrypted by default
   - Location: `client/src/utils/fileShareManager.js`

7. **Call Quality Monitoring**
   - Real-time bitrate, latency, packet loss display
   - LOCAL statistics only (not sent to server)
   - No analytics collection
   - Location: `client/src/components/CallQualityIndicator.jsx`

8. **Incoming Call Ringtone (NEW)**
   - Audio notification for incoming calls
   - Phone ringtone via Web Audio API
   - Vibration pattern on mobile
   - Location: `client/src/utils/ringtoneManager.js`

9. **End-to-End Encryption Module (NEW)**
   - AES-256-GCM symmetric encryption
   - RSA-2048 key exchange
   - HMAC signatures
   - Password hashing
   - Location: `client/src/utils/encryption.js`

10. **Data Deletion**
    - Messages exist only in memory
    - Auto-delete on disconnect
    - Room data erased from server immediately on user leave
    - Local storage cleared on app close
    - No persistent database

## What This App Does NOT Protect Against

### ❌ Out of Scope

1. **Kernel-Level Capture**
   - Apps with system-level access (Windows kernel, drivers)
   - Example: Rootkit, kernel module
   - Mitigation: Users must trust their OS

2. **Hardware Capture**
   - External camera pointed at screen
   - Physical recording (phone, camcorder)
   - Mitigation: Use physical camera covers

3. **Audio Capture (Hardware)**
   - Parabolic microphone, laser mic
   - Network-level monitoring (can't prevent)
   - Mitigation: Use white noise / physical isolation

4. **Compromised Device**
   - Malware on device before app launch
   - Compromised OS or drivers
   - Mitigation: Keep device clean, update Windows

5. **Network Monitoring**
   - ISP-level monitoring (legal order)
   - Employer/school network admin
   - Mitigation: Use VPN (separate product)

6. **Relay Server Logs** (Production)
   - If server admin keeps logs (against policy)
   - Compromised server
   - Mitigation: Run your own TURN/signaling server

## Security Headers & Policies

### Client Configuration

Electron security settings in `client/electron/main.js`:

```javascript
webPreferences: {
  preload: path.join(__dirname, 'preload.js'),
  nodeIntegration: false,              // ✓ Disabled
  contextIsolation: true,              // ✓ Enabled
  enableRemoteModule: false,           // ✓ Disabled
  sandbox: true,                       // ✓ Enabled
  disableBlinkFeatures: 'AutoplayPolicy',
}
```

### Server Configuration

Signaling server security in `server/server.js`:

```javascript
// ✓ CORS restricted to known client domain
cors: {
  origin: process.env.CLIENT_URL || 'http://localhost:5173',
  methods: ['GET', 'POST'],
}

// ✓ Minimal logging in production
const isDev = process.env.NODE_ENV !== 'production';
```

## Logging Policy

### What Gets Logged (Development Only)

```
[2024-11-14T10:30:45.123Z] INFO: User connected
[2024-11-14T10:30:46.234Z] INFO: Room created
  { roomCode: 'ABC12345', sessionId: '...' }
[2024-11-14T10:30:50.345Z] INFO: Message relayed
  { roomCode: 'ABC12345', messageLength: 45 }
[2024-11-14T10:31:00.456Z] INFO: ICE candidate relayed
  { roomCode: 'ABC12345' }
[2024-11-14T10:31:30.567Z] INFO: User disconnected
  { socketId: 'xyz', roomCode: 'ABC12345' }
```

### What Never Gets Logged

- ❌ Message content
- ❌ User identities
- ❌ Call recordings
- ❌ Device information
- ❌ IP addresses
- ❌ Timestamps of messages

### Purging Logs

In production, logs are **automatically discarded**. To manually purge dev logs:

```bash
# Clear application logs
rm -rf /var/log/anon-comm/

# Clear PM2 logs
pm2 flush

# Restart server
pm2 restart anon-comm
```

## Authentication & Authorization

### What's NOT Implemented

- ❌ User accounts
- ❌ Passwords
- ❌ OAuth / SSO
- ❌ API keys
- ❌ Tokens with persistence

### Why?

Accounts = Identification = Privacy loss. Ephemeral codes are sufficient.

### Room Access Control

```javascript
// Room only accessible by code holder
socket.on('join-room', (data) => {
  if (!rooms.has(roomCode)) {
    return callback({
      success: false,
      error: 'Room not found',
    });
  }
  
  // Code must be exact (case-sensitive, 8 chars)
  if (!/^[A-Z0-9]{8}$/.test(roomCode)) {
    return callback({ success: false });
  }
  
  // Only 2 people max per room
  if (room.participants.size >= 2) {
    return callback({
      success: false,
      error: 'Room is full',
    });
  }
  
  // No persistence of who joined = no audit trail
});
```

## Third-Party Dependencies Audit

### Client

| Package | Version | Why | Risk |
|---------|---------|-----|------|
| react | 18.2.0 | UI framework | Low |
| socket.io-client | 4.7.2 | Signaling | Low |
| framer-motion | 10.16.16 | Animations | Low |
| tailwindcss | 3.3.6 | Styling | None |
| electron | 27.0.0 | Desktop app | Medium (signing) |

### Server

| Package | Version | Why | Risk |
|---------|---------|-----|------|
| express | 4.18.2 | Web framework | Low |
| socket.io | 4.7.2 | WebSocket server | Low |
| cors | 2.8.5 | CORS middleware | Low |

**Risk Assessment**: All packages are from reputable maintainers. Update monthly for security patches.

## Vulnerability Disclosure

If you find a security issue:

1. **Do NOT** post publicly
2. Email: security@your-domain.com
3. Include:
   - Description of issue
   - Steps to reproduce
   - Potential impact
4. Allow 30 days for fix

## Compliance & Legal

### GDPR
- ✓ No personal data collected
- ✓ No storage = no right to be forgotten
- ✓ No tracking

### CCPA
- ✓ No data collection
- ✓ No third-party sharing
- ✓ No retention

### HIPAA
- ❌ Not compliant (requires additional controls)
- ✓ But can be self-hosted for HIPAA compliance

### FCC (Telecom)
- ✓ E2E encrypted optional
- ✓ Minimal call metadata
- ✓ No recording

## Incident Response

### If Server is Compromised

1. **Shut down immediately**
2. **Notify users** (via GitHub/email)
3. **Rotate certificates**
4. **Audit logs** for 7 days prior
5. **Re-deploy** from clean backup
6. **Update client** to new server URL

### If Client is Compromised (Malware)

This is **out of scope for the app**. Users must:
- Run antivirus scan
- Update Windows
- Reset credentials (change passwords on other services)

## Testing Security

### Manual Tests

```bash
# Test 1: Verify contentProtection
# Expected: Screen capture tools show black/protected window
# Tools: PrintScreen, Snip & Sketch, OBS, ShareX

# Test 2: Verify DevTools disabled
# Expected: F12, Ctrl+Shift+I does nothing
# Tools: Keyboard, DevTools shortcut

# Test 3: Verify no message persistence
# Expected: Reconnect shows no old messages
# Test: Send message → Disconnect → Reconnect → No message

# Test 4: Verify code is single-use
# Expected: Can't join same code twice
# Test: Create room with code ABC → Close → Try joining ABC → Fails

# Test 5: Verify contextIsolation
# Expected: window.require is undefined
# In DevTools console: window.require (should error)

# Test 6: Verify room auto-delete
# Expected: Server memory stays low after disconnect
# Monitor: ps aux | grep node (memory usage)
```

### Automated Tests

```javascript
// Test ephemeral code generation
import { generateRoomCode, isValidRoomCode } from './utils/crypto.js';

describe('Ephemeral Codes', () => {
  test('generates 8-character code', () => {
    const code = generateRoomCode();
    expect(code).toMatch(/^[A-Z0-9]{8}$/);
  });
  
  test('validates room code format', () => {
    expect(isValidRoomCode('ABC12345')).toBe(true);
    expect(isValidRoomCode('abc12345')).toBe(false);
    expect(isValidRoomCode('ABC1234')).toBe(false);
  });
  
  test('generates unique codes', () => {
    const codes = new Set();
    for (let i = 0; i < 1000; i++) {
      codes.add(generateRoomCode());
    }
    expect(codes.size).toBe(1000); // No collisions
  });
});

// Test message deletion
describe('Message Deletion', () => {
  test('clears messages on disconnect', () => {
    const room = createRoom();
    addMessage(room, 'test');
    expect(room.messages.length).toBe(1);
    
    socket.disconnect();
    expect(room.messages.length).toBe(0);
  });
});

// Test no persistence
describe('No Persistence', () => {
  test('does not save to database', () => {
    const room = createRoom();
    // Verify no DB writes
    expect(database.collections).not.toContain('messages');
    expect(database.collections).not.toContain('users');
  });
});
```

## Regular Security Audits

Schedule:
- **Weekly**: Check dependencies for vulnerabilities (`npm audit`)
- **Monthly**: Review server logs for anomalies
- **Quarterly**: Full security audit
- **Annually**: Third-party penetration test

## Privacy Policy Template

For deployment, use this template:

```
# Privacy Policy

## No Data Collection
This application collects no personal data. All messages and connection information
are ephemeral and exist only in memory during the session.

## Ephemeral Identifiers
Users are assigned temporary session IDs that are discarded when the app closes.
These IDs are not linked to any persistent identity.

## Message Handling
- Messages are transmitted in real-time to participants only
- Messages are NOT stored on servers
- Messages are deleted from memory immediately upon disconnect
- Users can never retrieve past messages

## Abuse Reports
When you submit an abuse report:
- Only the room code, timestamp, and a hash of the message snippet are stored
- Reports are stored for 7 days for review
- Reports are then permanently deleted
- Reports are never linked to your identity

## Server Logs
- Server logs are automatically purged daily in production
- Server logs contain no message content or personal data
- Access to server logs is restricted to administrators

## WebRTC Connections
- Direct peer-to-peer connections use DTLS-SRTP encryption
- Your IP address is disclosed to your peer (required for connection)
- Consider using a VPN if IP privacy is important

## No Tracking
This application contains:
- No analytics
- No cookies
- No third-party scripts
- No advertisements
- No data sharing

## Changes to This Policy
We may update this policy. Changes take effect upon app update.

Last updated: [Date]
```

---

**Version**: 1.0.0  
**Last Updated**: November 2024  
**Maintained By**: [Your Organization]

# Future Improvements & Enhancement Roadmap

This document outlines potential features and improvements for the anonymous communication app.

## Short-term (1-3 months)

### Performance
- [ ] Implement message virtual scrolling (for 100+ messages)
- [ ] Add connection quality indicator (good/fair/poor)
- [ ] Optimize bundle size (target < 100MB installer)
- [ ] Add lazy loading for components

### UX
- [ ] Typing indicator (show when peer is typing)
- [ ] "Read receipts" (optional, privacy-aware)
- [ ] Emoji picker in chat
- [ ] Drag-drop file sharing (ephemeral, no persistence)
- [ ] Copy room code button
- [ ] Sound notifications (toggle in settings)

### Features
- [ ] System tray icon for quick access
- [ ] Hotkey to launch app (e.g., Ctrl+Shift+A)
- [ ] Text-to-speech for accessibility
- [ ] Auto-focus audio device selection
- [ ] Recording indicator (when detected)

## Mid-term (3-6 months)

### End-to-End Encryption (E2EE)
```javascript
// Implementation outline:
// 1. Use TweetNaCl.js for encryption
// 2. ECDH key exchange via WebRTC
// 3. Encrypt messages before sending
// 4. Decrypt in receiver

import nacl from 'tweetnacl';

function generateKeyPair() {
  return nacl.box.keyPair();
}

function encryptMessage(message, recipientPublicKey, senderSecretKey) {
  const nonce = nacl.randomBytes(nacl.box.nonceLength);
  const encrypted = nacl.box(
    new TextEncoder().encode(message),
    nonce,
    recipientPublicKey,
    senderSecretKey
  );
  return { encrypted, nonce };
}
```

### Group Calls (3-4 people)
- [ ] Implement mesh topology (peer-to-peer for 3-4 people)
- [ ] For 5+ people, use SFU (Selective Forwarding Unit)
- [ ] Server-side SFU code (MediaSoup or Janus)

### Mobile Clients
- [ ] React Native iOS app
- [ ] React Native Android app
- [ ] Shared signaling server
- [ ] Cross-platform compatibility

## Long-term (6-12 months)

### Advanced Privacy
- [ ] Insertable streams for custom encryption
- [ ] Hardware security module (HSM) support
- [ ] Watermarking for shared media
- [ ] Anonymous proxy mode (Tor integration)

### Moderation
- [ ] On-device ML model for content filtering
- [ ] Community moderation (peer review of reports)
- [ ] Automated account ban system
- [ ] Appeal mechanism for false positives

### Features
- [ ] Screen sharing (encrypted peer-to-peer)
- [ ] Voice messages (ephemeral)
- [ ] Background blur/effects
- [ ] Custom virtual backgrounds
- [ ] Room scheduling (no email, just code + time)

### Infrastructure
- [ ] Geographic distribution (multiple signaling servers)
- [ ] Load balancing with geo-routing
- [ ] Metrics dashboard (privacy-preserving)
- [ ] Anomaly detection for abuse

## Integration Ideas

### With Third-Party Services
- [ ] SlackBot-style integration (not data sharing, just notifications)
- [ ] Discord bot to share room codes
- [ ] GitHub integration for organization use

### Accessibility
- [ ] Captions/subtitles (speech-to-text locally)
- [ ] Live transcription (on-device, no sending)
- [ ] Voice commands
- [ ] Screenreader improvements

## Research & Exploration

### Cryptography
- [ ] Explore post-quantum algorithms
- [ ] Implement double ratchet protocol (Signal-style)
- [ ] Perfect forward secrecy (PFS)

### WebRTC
- [ ] Simulcast (multiple video qualities)
- [ ] Adaptive bitrate control
- [ ] Network coding for packet loss recovery
- [ ] FEC (Forward Error Correction)

### Privacy
- [ ] Formal privacy audit
- [ ] Differential privacy in metrics
- [ ] Noise injection in stats
- [ ] Research room code collision probability

## Technical Debt

- [ ] Upgrade all dependencies quarterly
- [ ] Refactor large components (Chat.jsx > 300 lines)
- [ ] Add TypeScript (type safety)
- [ ] Increase test coverage (target > 80%)
- [ ] Add E2E tests (Playwright)
- [ ] Performance profiling setup

## Community & Ecosystem

### Open Source
- [ ] Create WebRTC signaling server library
- [ ] Publish RTCManager as standalone module
- [ ] Create Electron privacy boilerplate
- [ ] Documentation for deploying Coturn

### Plugins/Extensions
- [ ] Custom theme support
- [ ] Language/locale plugins
- [ ] Custom notification handlers
- [ ] Server-side room plugins

## Success Metrics

### Launch Goals
- [ ] 1,000+ downloads in first month
- [ ] 99.9% uptime
- [ ] < 50ms signaling latency
- [ ] < 200MB memory usage
- [ ] Zero data breaches

### User Adoption
- [ ] Identify use cases (activism, privacy-conscious users, etc.)
- [ ] Gather feedback
- [ ] Build community (Discord/Matrix)
- [ ] Regular updates (monthly)

## Version Timeline

```
v1.0.0 - November 2024
├─ Basic chat & calls
├─ Ephemeral codes
└─ Screen capture protection

v1.1.0 - December 2024
├─ Typing indicator
├─ Emoji support
├─ Settings panel
└─ Better error messages

v1.2.0 - January 2025
├─ End-to-End Encryption (optional)
├─ Group calls (3 people)
├─ File sharing (ephemeral)
└─ Screen sharing

v2.0.0 - Q2 2025
├─ Mobile clients (iOS/Android)
├─ Group calls (5+ with SFU)
├─ Advanced moderation
└─ Geographic distribution

v3.0.0 - Q4 2025
├─ Insertable streams E2EE
├─ Hardware acceleration
├─ Custom plugins
└─ Full federation
```

## Contributing

Developers interested in these features should:

1. **Open an issue** describing the feature
2. **Gather feedback** from community
3. **Create a feature branch** following git-flow
4. **Write tests** (unit + integration)
5. **Submit PR** with detailed description
6. **Code review** by maintainers
7. **Merge** after approval

### Contribution Guidelines

```
Good PR candidates:
✅ Bug fixes
✅ Documentation
✅ Performance improvements
✅ Accessibility enhancements
✅ Security improvements

Needs discussion first:
🤔 New features
🤔 API changes
🤔 Dependencies upgrades
🤔 Architecture changes
```

## Resources

### Learning
- WebRTC: https://webrtc.org/
- Electron: https://electronjs.org/docs
- React: https://react.dev/
- TweetNaCl.js: https://tweetnacl.js.org/

### Tools
- FFmpeg (audio/video processing)
- MediaSoup (SFU implementation)
- Janus (WebRTC gateway)
- OpenSTFU (screening/filtering)

### Communities
- WebRTC Discussion: https://github.com/w3c/webrtc-pc/issues
- Electron Discussions: https://github.com/electron/electron/discussions
- Privacy Research: https://www.eff.org/

---

**Last Updated**: November 2024
**Maintained By**: [Your Name/Organization]
**Community Chat**: [Link to Discord/Matrix]

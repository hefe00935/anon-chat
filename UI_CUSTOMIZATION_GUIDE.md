# 🎨 UI Customization & Settings Guide

## Overview

Your anonymous communication app now features a powerful settings system with multiple themes, GUI modes, and zen mode integration.

---

## ⚙️ Settings Panel (Top-Left)

### Location
The settings button (⚙️) is located at the **top-left corner** of your application.

### Features

#### 1. 🎨 Theme Selection

Choose from 4 theme options:

**Current Theme** (Default)
- Modern dark interface
- Purple and indigo accents
- Standard visibility
- Great for everyday use

**Dark Theme**
- Pure dark mode
- Slate and indigo colors
- Maximum contrast
- Easy on the eyes

**Light Theme** 
- Bright, clean interface
- Light grays and indigo
- Professional appearance
- Better for daytime use

**Beta Theme** ✨
- Cyberpunk neon aesthetic
- Cyan and magenta colors
- Futuristic glitch effects
- WARNING: Intense visual style

### 2. 🎮 GUI Mode Selection

Select how your interface looks:

**Classic Interface** (Default)
- Standard clean design
- Familiar layout
- Professional appearance
- Great for productivity

**Beta Interface** 🔮
- Full cyberpunk/neon transformation
- ALL elements have neon glow
- Glitch effects on button clicks
- Pulsing notifications
- Sharp angles (no rounded corners)
- Monospace font
- Aggressive color scheme

### 3. 🧘 Zen Mode

Minimalist, distraction-free interface for focused communication.

**Activation:**
- Click the Zen Mode toggle in settings, OR
- Press **Z** on your keyboard

**Features:**
- Hides non-essential UI elements
- Distraction-free calling
- Minimal message interface
- Hidden statistics by default

**Hotkeys in Zen Mode:**
- **Z** - Toggle zen mode on/off
- **C** - Show/hide chat
- **S** - Show/hide statistics
- **V** - Call controls menu
- **Ctrl+E** - Settings panel

---

## 🎯 Usage Guide

### Theme + GUI Mode Combinations

#### Dark Mode + Classic GUI
```
✓ Best for: Night use, standard work
✓ Appearance: Modern, professional
✓ Performance: Optimal
✓ Recommended: YES
```

#### Light Mode + Classic GUI
```
✓ Best for: Daytime use, outdoors
✓ Appearance: Clean, bright
✓ Performance: Good
✓ Note: May be harder on eyes at night
```

#### Beta Theme + Beta GUI
```
✓ Best for: Futuristic aesthetic lovers
✓ Appearance: Cyberpunk/neon style
✓ Performance: Normal (lots of shadows/glows)
✓ Warning: Intense colors - not for everyone
✓ Great for: Impressing friends, screenshots
```

#### Dark Theme + Beta GUI
```
✓ Best for: Cyberpunk look with better readability
✓ Appearance: Neon on dark background
✓ Performance: Good
✓ Recommended: For beta GUI enthusiasts
```

### Quick Switch Guide

1. **For Privacy Calls:**
   - Enable Zen Mode (Press Z)
   - Switch to Dark Theme
   - Minimal UI, focus on call

2. **For Public Presentation:**
   - Enable Beta GUI + Beta Theme
   - Impressive visual style
   - Draw attention and interest

3. **For Normal Chatting:**
   - Current Theme + Classic GUI (default)
   - Balanced, comfortable
   - Standard interface

4. **For Night Work:**
   - Dark Theme + Classic GUI
   - Professional appearance
   - Eye-friendly

---

## 🔮 Beta GUI Deep Dive

### Visual Features

**Neon Glow Effects:**
```
All buttons glow with cyan color
Hover effects add magenta glow
Creates a cyberpunk atmosphere
```

**Sharp Design:**
```
All corners are squared (no border-radius)
Clean geometric aesthetic
Futuristic appearance
```

**Monospace Font:**
```
Everything uses Courier New
Looks like a hacker's terminal
Increases immersion
```

**Glitch Animation:**
```
Click any button in beta mode
Momentary visual glitch
Adds character and excitement
```

**Neon Pulse:**
```
Notifications pulse with dual-color glow
Grabs attention immediately
Cyan and magenta colors
```

### Color Palette

```
Primary:   #00d4ff (Cyan)
Secondary: #ff006e (Magenta)
Accent:    #00f5ff (Bright Cyan)
Dark:      #1a1a2e (Very Dark Blue)
Darker:    #0f0f1e (Almost Black)
```

### CSS Variables

In beta mode, you can use these CSS variables:
```css
var(--beta-primary)    /* #00d4ff - Main cyan */
var(--beta-secondary)  /* #ff006e - Magenta accent */
var(--beta-accent)     /* #00f5ff - Bright cyan */
var(--beta-dark)       /* #1a1a2e - Dark background */
var(--beta-darker)     /* #0f0f1e - Darker background */
```

---

## 🧘 Zen Mode Details

### What Gets Hidden?

When Zen Mode is active:

```
✓ Hidden by default:
  - Non-essential UI elements
  - Status bars (unless toggled)
  - Message reactions (unless toggled)
  - Extra information panels

✓ Visible by default:
  - Message input
  - Call controls
  - Current conversation
  - Essential information only
```

### Zen Mode Hotkeys

| Key | Action |
|-----|--------|
| **Z** | Toggle Zen Mode |
| **C** | Show/Hide Chat |
| **S** | Show/Hide Stats |
| **V** | Call Controls Menu |
| **Ctrl+E** | Settings Panel |

### Zen Mode Best Practices

1. **For Privacy Calls:**
   - Enable Zen Mode
   - Use keyboard shortcuts to toggle what's visible
   - Minimal distractions during sensitive calls

2. **For Focus Messaging:**
   - Enable Zen Mode
   - Hide stats (press S)
   - Just chat, nothing else

3. **For Presentations:**
   - Use normal mode
   - Enable Beta GUI for visual impact
   - Show diagnostics panel when needed

---

## 💾 Settings Persistence

All settings are automatically saved to your browser's local storage:

```javascript
// Theme is saved as:
localStorage.getItem('app-theme')     // 'dark', 'light', 'beta', 'current'

// GUI mode is saved as:
localStorage.getItem('app-gui-mode')  // 'classic', 'beta'
```

**Note:** Settings persist across browser restarts and sessions.

---

## 🚀 Tips & Tricks

### Tip 1: Instant Theme Switch
Click the theme buttons to instantly switch themes without page reload.

### Tip 2: Zen Mode for Calls
Activate Zen Mode before accepting a call for maximum focus:
```
1. Press Z to enable Zen Mode
2. Accept incoming call
3. Minimize UI distractions
4. Press Z again to exit
```

### Tip 3: Beta GUI for Screenshots
Want impressive screenshots?
```
1. Switch to Beta Theme
2. Enable Beta GUI Mode
3. Click the Diagnostics Panel
4. Take your screenshot
5. Impress your friends!
```

### Tip 4: Hotkey Navigation
Master the hotkeys for quick navigation:
```
Z       - Toggle Zen Mode
Ctrl+E  - Open Settings
S       - Show Statistics
C       - Show Chat
V       - Call Controls
```

### Tip 5: Keyboard-Only Usage
In Zen Mode, you can control everything with hotkeys:
```
No mouse needed - all keyboard accessible
Perfect for accessibility
Great for power users
```

---

## 🎨 Customization Ideas

### Create Your Own Theme

You can extend the settings system by:

1. Adding new theme files to `client/src/utils/themeManager.js`
2. Creating custom CSS palettes
3. Defining new color schemes

### Future Enhancements

Planned customizations:
- [ ] Custom color picker
- [ ] Font size adjustment
- [ ] UI element opacity control
- [ ] Layout customization
- [ ] More GUI modes (terminal, minimal, modern, retro)

---

## 🐛 Troubleshooting

### Theme Not Applying?
1. Clear browser cache: Ctrl+Shift+Delete
2. Close and reopen the app
3. Check browser console for errors: F12

### Zen Mode Hotkeys Not Working?
1. Make sure focus is in the app window
2. Check if another app is intercepting hotkeys
3. Try toggling zoom (Ctrl+0 to reset)

### Beta GUI Too Intense?
1. Switch back to "Current Theme" or "Light Theme"
2. Disable Beta GUI Mode → select "Classic Interface"
3. Zen Mode can hide some visual elements

### Settings Not Saving?
1. Check if local storage is enabled
2. Try incognito mode to test
3. Check browser privacy settings

---

## 📊 Performance Notes

| Mode | CPU Usage | GPU Usage | Memory |
|------|-----------|-----------|--------|
| Dark Theme + Classic | Low | Low | Low |
| Light Theme + Classic | Low | Low | Low |
| Beta Theme + Classic | Low | Medium | Low |
| Beta Theme + Beta GUI | Medium | High | Medium |
| Zen Mode | Low | Low | Low |

**Recommendation:** 
- For older devices: Dark Theme + Classic GUI
- For modern devices: Any combination works great
- For presentations: Beta Theme + Beta GUI

---

## 🔒 Privacy Note

All theme and GUI settings are stored **locally** in your browser:
- ✅ No data sent to server
- ✅ No analytics tracking
- ✅ Settings are completely private
- ✅ Can be cleared anytime

---

## 📞 Quick Access

**Settings Button Location:** Top-left corner ⚙️  
**Diagnostics Button Location:** Bottom-right corner 🔍  
**Zen Mode Hotkey:** Press Z

---

## 🎉 Enjoy Customization!

Your anonymous communication app is now fully customizable with:
- ✅ 4 theme options
- ✅ 2 GUI modes  
- ✅ Zen mode for focus
- ✅ Full hotkey support
- ✅ Persistent settings

Have fun exploring! 🚀

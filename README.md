# MacTermy

A native macOS SSH client and terminal manager. Supports SSH terminals, RDP remote desktop with clipboard and drive sharing, SCP file transfers, and multi-session command broadcasting — all in a single self-contained app.

**Author:** Yaniv Hemi | **License:** MIT

---

## Features

### SSH Terminal

- Full terminal emulation (xterm-256color) with ANSI escape sequence parsing
- 256-color and 24-bit truecolor rendering
- Bold, dim, underline, inverse text attributes
- 5000-line scrollback buffer with alternate screen support
- Password and private key authentication
- Automatic terminal resize with SIGWINCH signaling
- Command history navigation with arrow keys
- Multiple simultaneous sessions with tabbed interface
- SSH keepalive (ServerAliveInterval=60s) and connect timeout (10s)

### RDP Remote Desktop

- Built-in RDP client powered by FreeRDP 3.22 (no external apps required)
- BGRA32 pixel rendering with CoreGraphics integration
- Aspect-fit display scaling
- Default 1920x1080 desktop with dynamic resizing
- Full keyboard input with macOS-to-RDP scancode translation
- All modifier keys, function keys, and extended key support
- Mouse: left/right/middle click, drag, vertical and horizontal scroll
- NLA, TLS, and RDP security negotiation
- Domain authentication support
- Automatic certificate acceptance

### RDP Clipboard

- Bidirectional text clipboard sync between macOS and Windows
- Cmd+C/V/X/A automatically mapped to Ctrl+C/V/X/A in the remote session
- FreeRDP cliprdr channel with CF_UNICODETEXT format
- UTF-16LE/UTF-8 conversion with Windows/macOS line ending normalization

### RDP Drive Sharing

- Share a local Mac folder as a mapped drive in the remote Windows session
- Accessible as `\\tsclient\MacShare` in Windows Explorer
- Configured per-connection via the shared folder path field
- FreeRDP rdpdr channel with patched state machine for broad Windows server compatibility

### SCP File Transfer

- Upload and download files between Mac and remote servers
- Remote file browser with directory navigation
- File type icons: folders, executables, archives, configs, logs, source code
- Batch file selection and transfer
- Password and private key authentication

### SFTP Browser

- Sidebar-integrated remote file browser for active SSH sessions
- Double-click directory navigation
- Path display with refresh

### MultiExec / Broadcast Mode

- Execute commands simultaneously across multiple SSH sessions
- Auto-layout grid adapts from 1x1 up to dynamic NxM based on session count
- Per-session terminal output tiles with ANSI color support
- Select/deselect individual sessions with checkboxes
- Real-time output display across all tiles
- Click tiles to highlight and select

### Connection Manager

- Save, edit, and organize SSH, RDP, and SCP connections
- Sidebar grouped by connection type with search and filter
- Quick connect bar for one-off sessions without saving
- Connection timestamps (created, last used)
- Persistent storage to JSON

### Settings

- Configurable default SSH and RDP ports
- Terminal font selection: SF Mono, Menlo, Monaco, Courier, Courier New
- Font size slider (9–24pt)
- Auto-save and notification toggles

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| Cmd+N | New session |
| Cmd+Shift+T | New SCP transfer |
| Cmd+Shift+W | Disconnect all sessions |
| Cmd+V | Paste (SSH terminal) / Ctrl+V (RDP) |
| Cmd+C | Ctrl+C (RDP) |
| Cmd+A | Ctrl+A (RDP) |
| Cmd+X | Ctrl+X (RDP) |
| Up/Down | Command history (SSH terminal) |

---

## Installation

1. Download `MacTermy-v1.0.0.zip`
2. Extract the archive
3. Move `MacTermy.app` to `/Applications`
4. On first launch, right-click the app and select **Open** (macOS Gatekeeper)

All dependencies (FreeRDP, OpenSSL, codecs) are bundled inside the app. No Homebrew or additional installations required.

---

## System Requirements

- macOS 13.0 (Ventura) or later
- Apple Silicon (arm64)

---

## Building from Source

### Prerequisites

```bash
brew install freerdp openssl@3 cmake
```

### Build

```bash
cd MacTermy
swift build -c release
```

### Package

```bash
./package.sh
```

This produces `MacTermy-v1.0.0.zip` with all dependencies bundled.

---

## Architecture

```
MacTermy.app/
├── Contents/
│   ├── MacOS/
│   │   ├── MacTermy          # Main executable (SwiftUI)
│   │   └── sshpass            # SSH password utility
│   ├── Frameworks/            # Bundled dynamic libraries
│   │   ├── libfreerdp*.dylib  # FreeRDP 3.22 (patched for drive sharing)
│   │   ├── libwinpr3.dylib    # WinPR runtime
│   │   ├── libssl/libcrypto   # OpenSSL 3
│   │   ├── libcjson.1.dylib   # JSON parser
│   │   ├── libusb-1.0.0.dylib # USB support
│   │   └── ...                # Codec and utility libraries
│   ├── Resources/
│   │   └── AppIcon.icns
│   └── Info.plist
```

### Source Layout

| Path | Description |
|------|-------------|
| `Sources/MacTermyApp.swift` | App entry point, menu bar, window management |
| `Sources/Views/MainView.swift` | Main split view layout (sidebar + tabs + content) |
| `Sources/Views/TerminalTabView.swift` | SSH terminal view with ANSI rendering |
| `Sources/Views/RDPView.swift` | RDP display rendering and input handling |
| `Sources/Views/SCPView.swift` | SCP file transfer dual-pane browser |
| `Sources/Views/SidebarView.swift` | Connection list and SFTP browser |
| `Sources/Views/MultiExecGridView.swift` | Broadcast command grid |
| `Sources/Views/SessionSheet.swift` | New/edit connection dialog |
| `Sources/Views/SettingsView.swift` | Application settings |
| `Sources/Models/Connection.swift` | Connection data model |
| `Sources/Models/ConnectionManager.swift` | Save/load/manage connections |
| `Sources/Models/SSHSession.swift` | SSH session with PTY management |
| `Sources/Models/RDPSession.swift` | RDP session with FreeRDP callbacks |
| `Sources/Models/TerminalEmulator.swift` | ANSI terminal state machine |
| `Sources/CFreeRDP/freerdp_bridge.c` | C bridge to FreeRDP library |
| `Sources/CFreeRDP/include/freerdp_bridge.h` | C bridge header |

---

## Security Notes

- Passwords are stored in plaintext in `~/Documents/mactermy_connections.json`. Use SSH key authentication for sensitive environments.
- SSH strict host key checking is disabled by default for convenience.
- RDP certificates are auto-accepted.

---

## License

MIT License

Copyright (c) 2026 Yaniv Hemi

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

---

**MacTermy** — Built with SwiftUI.

## cloak browser (Rust + Wry/WebView2)

Local-first minimal desktop browser focused on privacy. Runs entirely on your machine, embeds the system WebView (Edge WebView2 on Windows), and stores state locally.

### Features
- Tabs with persistent session (up to 30 tabs)
- Address bar with search (DuckDuckGo by default)
- Always-on-top tab bar rendered via shadow DOM so sites cannot hide it
- Persistent cookies/localStorage/cache (WebView2 profile)
- No telemetry, no external analytics

### Requirements
- Rust (stable)
- Windows 10/11 with the Microsoft WebView2 Runtime installed
  - Download: [WebView2 Runtime](https://developer.microsoft.com/en-us/microsoft-edge/webview2/)

### Build and Run
```powershell
cd minimal_browser
cargo run
```

Build a release binary:
```powershell
cargo build --release
```

Default homepage is DuckDuckGo. Use the address bar to navigate or search. The tab bar stays pinned to the top.

### Persistence (where data is stored)
- App data directory (profile, tabs/bookmarks/history):
  - Windows: `%APPDATA%/com/minimal/browser/` (from Rust `directories::ProjectDirs::data_dir()`)
  - File: `profile.json`
- WebView (cookies/localStorage/cache):
  - Windows: `%LOCALAPPDATA%/com/minimal/browser/wv2_profile/` (set via `WEBVIEW2_USER_DATA_FOLDER`)

You can back up or clear session data by copying/deleting these folders/files while the app is closed.

### Controls
- Address bar: type a URL (adds `https://` if missing) or a search query
- Go button or Enter: navigate the active tab
- + Tab: open a new tab (limit 30)
- × on a tab: close the tab

### Configuration
- Start page: change `start_url` in `src/main.rs` if you prefer a different homepage
- UI: the injected UI is in `src/ui.js`. Tweak styles/behavior there

### Troubleshooting
- Build errors like “Access is denied (os error 5)”: close the running browser window before rebuilding
- Message “Failed to unregister class Chrome_WidgetWin_0. Error = 1412”: benign Chromium shutdown log; safe to ignore
- If the tab bar isn’t visible on a particular site, it’s likely due to restrictive CSP. The app’s UI is injected with a shadow DOM to avoid most clashes, but if you hit a problem site, open an issue or switch the start page, and we’ll consider moving the bar to a native (non-injected) layout

### Security & Privacy Notes
- This app itself does not add trackers or analytics
- Sites you visit still run their own code; use your preferred privacy settings and blockers at the network/OS level if needed

### Project Structure
- `src/main.rs`: Rust app entry, WebView and persistence wiring
- `src/ui.js`: UI logic for the tab bar, address bar, IPC to save profile

### License
No license specified yet. Add one if you plan to distribute.

# Minimal Privacy Browser

A lightweight, privacy-focused web browser built with Rust and WebView technology.

## Features

- **Fast & Lightweight**: Built with Rust for performance and memory safety
- **Privacy-First**: Minimal data collection and tracking protection
- **Modern UI**: Clean, responsive interface with dark/light themes
- **Settings panel**: Startup mode (Restore/Home), default search engine, home page
- **Adjustable UI**: Font size/weight sliders and compact/comfortable density
- **Tab Management**: Efficient tab handling with drag-and-drop support
- **Bookmark System**: Organized bookmark management with folders and tags
- **History Tracking**: Local browsing history with search capabilities
- **Session Restore**: Saves last session and restores tabs on startup (native IPC)
- **Persistent Sessions**: Stable WebView2 profile keeps cookies and logins between launches
- **HTTPS-Only (toggle)**: Upgrades http links to https where possible
- **Basic Tracker Blocking (toggle)**: Blocks common analytics/ads hosts
- **Cross-Platform**: Works on Windows, macOS, and Linux

## Security Features

### Protocol Security
- **Dangerous Protocol Blocking**: Automatically blocks `javascript:`, `data:`, `file:`, and other dangerous protocols
- **HTTPS Enforcement**: Prioritizes secure connections
- **Navigation Filtering**: Validates all URLs before navigation

### Content Security
- **Content Security Policy (CSP)**: Strict CSP headers with sandboxing
- **XSS Protection**: Built-in XSS filtering and prevention
- **Clickjacking Protection**: X-Frame-Options headers
- **MIME Type Protection**: Prevents MIME type sniffing attacks

### Data Security
- **Input Validation**: All user inputs are validated and sanitized
- **IPC Security**: Secure inter-process communication with validation
- **File Access Control**: Limited and validated file operations
- **Memory Safety**: Rust's memory safety guarantees

### Privacy Protection
- **Local Storage**: All data stored locally, no cloud sync
- **No Telemetry**: Zero tracking or analytics
- **Ad Blocking Ready**: Framework for content filtering
- **Fingerprinting Protection**: Anti-fingerprinting measures

## Security Improvements Made

### Before (Security Issues)
- Allowed dangerous protocols (`javascript:`, `data:`, `file:`, etc.)
- No Content Security Policy
- Weak input validation
- XSS vulnerabilities through `innerHTML`
- Unrestricted IPC commands
- Missing security headers

### After (Security Enhanced)
- ✅ Protocol filtering and validation
- ✅ Strict Content Security Policy
- ✅ Input sanitization and validation
- ✅ XSS prevention measures
- ✅ Secure IPC with validation
- ✅ Security headers (CSP, X-Frame-Options, etc.)
- ✅ URL validation and sanitization
- ✅ Request size limits
- ✅ Command whitelisting

## Installation

### Prerequisites
- Rust 1.70+ ([Install Rust](https://rustup.rs/))
- WebView2 (Windows) or system WebView (macOS/Linux)

### Build from Source
```bash
git clone <repository-url>
cd minimal_browser
cargo build --release
```

### Run
```bash
# Start with default search engine
cargo run

# Start with specific URL
cargo run https://example.com

# Start with search query
cargo run "search query"
```

## Configuration

### Environment Variables
- `RUST_LOG`: Set logging level (debug, info, warn, error)
- `MB_DATA_DIR`: Custom data directory path

### Data Storage
- **Location**: `~/.local/share/Cloak/CloakBrowser/profile/` (Linux/macOS) or `%APPDATA%\Local\Cloak\CloakBrowser\profile\` (Windows)
- **File**: `profile.json`
  - Structure: `{ tabs, active, bookmarks, history, prefs }`
  - `prefs`: `{ theme, fontScale, fontWeight, density, home, engine, startup }`
  - Written by Rust on `profile_save` IPC messages; read on startup

### Settings
- Open the top-right menu (☰) → Settings
- Configure:
  - Startup: Restore last session or open Home page
  - Home page URL
  - Default search engine (DuckDuckGo/Google/Bing/Brave)
  - Font size/weight, density (compact/comfortable)
  - Theme (dark/light)

## Customization

### Themes
- **Dark Theme**: Default dark interface
- **Light Theme**: Alternative light interface
- **Custom**: Modify theme colors in `themes` object

### Keyboard Shortcuts
- `Ctrl+T`: New tab
- `Ctrl+W`: Close tab
- `Ctrl+R`: Reload page
- `Ctrl+L`: Focus address bar
- `Ctrl+M`: Open menu
- `Alt+←/→`: Navigate back/forward

## 🧪 Security Testing

### Automated Testing
```bash
# Run security-focused tests
cargo test --features security

# Check for known vulnerabilities
cargo audit

# Security linting
cargo clippy -- -D warnings
```

### Manual Testing
- Test protocol blocking (try `javascript:alert('test')`)
- Verify CSP headers in browser dev tools
- Test XSS prevention with malicious inputs
- Validate IPC command restrictions

## 📋 Security Checklist

- [x] Protocol filtering implemented
- [x] Content Security Policy configured
- [x] Input validation and sanitization
- [x] XSS protection measures
- [x] Secure IPC communication
- [x] Security headers set
- [x] URL validation implemented
- [x] Request size limits enforced
- [x] Command whitelisting active
- [x] Memory safety (Rust)
- [x] No dangerous eval() usage
- [x] Secure defaults configured
## Contributing

### Security Contributions
We welcome security-focused contributions:

- Security code reviews
- Vulnerability research
- Security testing
- Documentation improvements
- Security feature implementations

### Development Guidelines
- Follow Rust security best practices
- Implement defense-in-depth
- Validate all inputs
- Sanitize all outputs
- Use secure defaults
- Document security decisions

## Security Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Rust Security](https://github.com/rust-lang/rust-security)
- [Browser Security](https://browser-security.net/)
- [Web Security](https://web.dev/security/)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [Wry](https://github.com/tauri-apps/wry) - WebView bindings
- [Tao](https://github.com/tauri-apps/tao) - Window management
- [Rust Security Working Group](https://github.com/rust-lang/rust-security) - Security guidance

## Roadmap

### Security Enhancements
- [ ] Certificate pinning
- [ ] DNS-over-HTTPS support
- [ ] Enhanced sandboxing
- [ ] Malware protection
- [ ] Phishing detection
- [ ] Encrypted storage

### Privacy Features
- [ ] Basic ad blocking (filter lists)
- [ ] Tracker blocking
- [ ] Incognito mode
- [ ] Privacy controls
- [ ] Data export/import

### UX / Features
- [ ] Per-site controls (JS, images, cookies)
- [ ] Native download manager
- [x] Startup behavior: Open home page / Restore last session (toggle)
- [x] Default search engine switcher in Settings (DDG, Google, Bing, Brave)
- [ ] Home page editor

### Persistence
- [x] Session restore persisted to `profile.json` via IPC
- [x] Periodic autosave and save-on-exit
- [ ] Migrate old localStorage data into `profile.json`

## How it works (IPC bridge)
- On startup, Rust reads `profile.json` and injects it into the page as `window.__MB_INITIAL__`.
- The UI updates state and preferences, then sends `window.ipc.postMessage({ cmd: 'profile_save', payload })` to persist.
- On Windows the runtime is WebView2 (Edge engine) with a modern user‑agent.

### Performance
- [ ] HTTP/2 support
- [ ] Compression handling
- [ ] Advanced caching
- [ ] Memory optimization

---

**Security Notice**: This browser is designed with security in mind, but no software is 100% secure. Always keep your system and dependencies updated, and report any security issues you discover.


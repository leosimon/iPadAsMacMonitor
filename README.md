# iPad as Mac Monitor — Enable Sidecar

> 🇨🇳 [中文说明请点这里 →](README_zh.md)

A one-click Automator app that automatically connects your iPad as a second display via **macOS Sidecar** — no manual navigation through System Settings required.

---

## ✨ What's New in v2.0

| | v1.0 | v2.0 |
|---|---|---|
| macOS 26 (Tahoe) support | ❌ | ✅ |
| Backward compatible (macOS 13–25) | ✅ | ✅ |
| UI detection method | Hardcoded menu path | URL scheme + recursive accessibility tree search |
| Device matching | Exact name match | Fuzzy match (contains keyword) |
| Sound feedback | ✅ | ✅ |
| Auto-close System Settings | ✅ | ✅ |

**v2.0 completely rewrites the UI automation logic.** Instead of navigating through fixed menu bar paths (which break when Apple changes the UI), it now:
1. Opens Display settings directly via system URL scheme
2. Recursively scans the accessibility tree to find the dropdown button — regardless of how Apple restructures the UI in future macOS versions

---

## 📋 Requirements

- Mac running **macOS 13 Ventura or later** (including macOS 26)
- iPad that supports Sidecar (iPad Pro, iPad Air 3rd gen+, iPad mini 5th gen+, iPad 6th gen+)
- Mac and iPad on the **same Wi-Fi network**, or connected via **Bluetooth**
- Signed in to the **same Apple ID** on both devices
- Accessibility permission granted to Automator

---

## 📥 Download

Download the latest version from the **[Releases](https://github.com/leosimon/iPadAsMacMonitor/releases)** page:

| Language | File |
|---|---|
| English | `Enable Sidecar.app.zip` |
| 中文 | `开启随航.app.zip` |

---

## 🚀 How to Use

### Step 1 — Re-export the app from Automator (required, one-time only)

Because this app is distributed as an Automator workflow, macOS requires you to export it locally on your own Mac before running it. This ensures the app is signed with your machine's identity and avoids the "damaged" error.

1. **Download** the zip file and unzip it
2. **Right-click** `Enable Sidecar.app` → **Open With → Automator**
3. In Automator, go to **File → Export**
4. Set the format to **Application**, choose a save location (e.g. Applications folder)
5. At the bottom, set **Code Sign** to **Ad-hoc Code Sign**
6. Click **Save**

> **Why is this step needed?**
> The `.app` file in the zip was built on a different Mac. macOS checks the code signature
> against the machine it was built on. Re-exporting with Ad-hoc Code Sign creates a fresh
> local signature that your Mac trusts — no paid Apple Developer account required.

---

### Step 2 — Grant Accessibility permission (first run only)

1. **Run** the exported app
2. macOS will prompt for **Accessibility permission** — click **"Open System Settings"**
3. In **System Settings → Privacy & Security → Accessibility**, enable the app
4. **Important:** do not dismiss this dialog without granting permission — if you accidentally deny it, see the troubleshooting section below

> After granting permission once, the app runs silently every time with no prompts.

---

### Step 3 — Run

Double-click the app — your iPad will connect as a second display within a few seconds.

### Sound Feedback
- 🔊 **Pop** — script started
- 🔊 **Blow** — iPad connected successfully
- 🔊 **Hero** — connection failed (iPad not found or not available)

---

### Troubleshooting: app keeps failing after denying Accessibility permission

If you accidentally dismissed the Accessibility prompt without granting permission, macOS caches the denial and **will not prompt again** — even if you delete and re-export the app. Fix it with one of these methods:

**Option A — Add manually in System Settings (easiest)**
1. Go to **System Settings → Privacy & Security → Accessibility**
2. Click **`+`** and manually add the exported app
3. Make sure the toggle is **on**

**Option B — Reset via Terminal**
```bash
# First find your app's Bundle ID
mdls -name kMDItemCFBundleIdentifier /Applications/Enable\ Sidecar.app

# Then reset only that app's permission
tccutil reset Accessibility com.apple.automator.Enable-Sidecar
```
After running this, launch the app again — the permission dialog will reappear.

---

## ⚙️ Customization

By default, the script matches any device whose name **contains "iPad"**. If you have multiple iPads, open the `.wflow` file in Automator and change this line:

```applescript
set TARGET_DEVICE_NAME to "iPad"
```

Replace `"iPad"` with your specific iPad's name (e.g., `"Leo's iPad Pro"`).

---

## 📁 Repository Structure

```
iPadAsMacMonitor/
├── Enable Sidecar.app.zip   ← v2.0 (English)
├── 开启随航.app.zip          ← v2.0 (Chinese)
├── archive/
│   ├── Sidecar.app.zip      ← v1.0 (English, archived)
│   └── 开启随航.app.zip      ← v1.0 (Chinese, archived)
├── README.md                ← This file (English)
└── README_zh.md             ← Chinese documentation
```

---

## 📝 Version History

| Version | Date | Notes |
|---|---|---|
| v2.0 | 2026-02 | Rewritten for macOS 26 compatibility; fuzzy device matching; recursive UI detection |
| v1.0 | 2025-05 | Initial release; hardcoded menu path navigation |

---

## 🙏 Contributing

Feel free to open an issue if the script stops working after a macOS update — the accessibility tree structure can change between versions.

---

## 🔍 How It Works

The app is an **Automator application** containing a single "Run AppleScript" action. Here is what happens step by step when you launch it.

### Execution Flow

```
Launch app
    │
    ▼
① Play "Pop" sound                    ← signals script has started
    │
    ▼
② Open Display settings               ← via URL scheme:
   x-apple.systempreferences:            no menu bar navigation needed
   com.apple.preference.displays
    │
    ▼
③ Wait for System Settings window     ← polls every 0.1s, up to 5s timeout
    │
    ▼
④ Wait 1 second                       ← lets the Display panel fully render
    │
    ▼
⑤ Recursively scan accessibility tree ← searches UI elements up to 4 levels deep
   to find the dropdown button           works with menu button / button / pop up button
    │
    ├─ Not found ──────────────────────► Play "Hero" sound (failure) → exit
    │
    ▼
⑥ Click the dropdown button           ← opens the "Add Display" menu
    │
    ▼
⑦ Wait for menu items to load         ← polls every 0.1s, up to 2s timeout
    │
    ▼
⑧ Read all menu item names            ← tries title → name → AXTitle for each item
    │
    ▼
⑨ Find last item containing "iPad"    ← searches from the bottom up:
                                         if Universal Control is also active,
                                         the same iPad appears twice —
                                         Sidecar is always the last entry
    │
    ├─ Not found ──────────────────────► Play "Hero" sound (failure) → exit
    │
    ▼
⑩ Click the matching menu item        ← initiates Sidecar connection
    │
    ▼
⑪ Play "Blow" sound                   ← connection initiated successfully
    │
    ▼
⑫ Wait 0.3s → Quit System Settings   ← restore volume to original level
```

### Helper Functions

| Function | Purpose |
|---|---|
| `playSound(soundPath)` | Plays a system sound via `afplay` shell command |
| `waitForApplicationWindow(appName, maxWait)` | Polls until the named app's window appears, with timeout |
| `openDisplaysSettings()` | Opens Display settings via URL scheme, returns success/failure |
| `findMenuButtonOrDropdownButton(element, depth)` | Recursively searches the accessibility tree for any kind of dropdown button |
| `waitForMenu(menuButton, maxWait)` | Polls until the menu has loaded at least one item |
| `findLastTargetIndexContains(text, list)` | Searches a list from the end, returns index of last item containing the target text |
| `getMenuItemText(menuItem)` | Reads a menu item's display text, trying `title` → `name` → `AXTitle` in order |
| `findAndClickTargetDevice(menuButton)` | Combines the above to locate and click the correct iPad menu entry |
| `getFirstWindow()` | Returns the first window of System Settings |

### Why v2.0 is More Robust Than v1.0

| Aspect | v1.0 approach | v2.0 approach |
|---|---|---|
| Opening settings | Navigate menu bar: View → Displays | Direct URL scheme — immune to menu bar changes |
| Finding the button | Hardcoded UI path (`group 1 of group 2 of splitter group 1 …`) | Recursive accessibility tree scan — works regardless of layout |
| Device matching | Exact string match (must type iPad name perfectly) | Fuzzy `contains` match — any name with "iPad" works |
| Duplicate handling | Not handled | Searches from the end — Sidecar always wins over Universal Control |
| Error handling | Single `try/on error` block | Each stage has its own timeout and fallback |

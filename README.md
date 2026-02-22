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

1. **Download** the zip file and unzip it
2. **Move** `Enable Sidecar.app` to your Applications folder (or anywhere you like)
3. **Run it once** — macOS will ask for Accessibility permission:
   - Go to **System Settings → Privacy & Security → Accessibility**
   - Enable **Automator** (or the app itself)
4. **Run again** — your iPad should connect as a second display within a few seconds

### Sound Feedback
- 🔊 **Pop** — script started
- 🔊 **Blow** — iPad connected successfully
- 🔊 **Hero** — connection failed (iPad not found or not available)

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

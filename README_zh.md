# iPad 作为 Mac 副屏 — 开启随航

> 🇺🇸 [English documentation →](README.md)

一键自动通过 **macOS 随航（Sidecar）** 将 iPad 连接为 Mac 副屏，无需手动打开系统设置进行操作。

---

## ✨ v2.0 新特性

| | v1.0 | v2.0 |
|---|---|---|
| 支持 macOS 26 (Tahoe) | ❌ | ✅ |
| 向下兼容（macOS 13–25） | ✅ | ✅ |
| UI 检测方式 | 硬编码菜单路径 | URL Scheme + 递归无障碍树查找 |
| 设备匹配方式 | 精确名称匹配 | 模糊匹配（包含关键词即可） |
| 音效反馈 | ✅ | ✅ |
| 自动关闭系统设置 | ✅ | ✅ |

**v2.0 完全重写了 UI 自动化逻辑。** 不再依赖固定菜单路径（每次 Apple 修改 UI 结构都会失效），现在改为：
1. 通过系统 URL Scheme 直接打开显示器设置
2. 递归扫描无障碍树，自动定位下拉按钮——无论 Apple 在未来版本中如何调整界面结构都能适配

---

## 📋 使用要求

- Mac 运行 **macOS 13 Ventura 或更新版本**（包括 macOS 26）
- iPad 支持随航功能（iPad Pro、iPad Air 第三代及以上、iPad mini 第五代及以上、iPad 第六代及以上）
- Mac 和 iPad 连接在**同一 Wi-Fi 网络**，或通过**蓝牙**连接
- 两台设备使用**同一 Apple ID** 登录
- 已授予 Automator 辅助功能（无障碍）权限

---

## 📥 下载

请在 **[Releases 页面](https://github.com/leosimon/iPadAsMacMonitor/releases)** 下载最新版本：

| 语言 | 文件名 |
|---|---|
| English | `Enable Sidecar.app.zip` |
| 中文 | `开启随航.app.zip` |

---

## 🚀 使用方法

1. **下载** zip 文件并解压
2. 将 `开启随航.app` **移动**到应用程序文件夹（或任意位置）
3. **首次运行**时，macOS 会请求辅助功能权限：
   - 前往 **系统设置 → 隐私与安全性 → 辅助功能**
   - 启用 **Automator**（或该 app 本身）
4. **再次运行**，几秒后 iPad 即会作为副屏连接

### 音效说明
- 🔊 **Pop（弹出音）** — 脚本开始执行
- 🔊 **Blow（吹风音）** — iPad 连接成功
- 🔊 **Hero（英雄音）** — 连接失败（未找到设备或设备不可用）

---

## ⚙️ 个性化设置

默认情况下，脚本会匹配名称**包含"iPad"**的设备。如果你有多台 iPad，请用 Automator 打开 `.wflow` 文件，修改以下这行：

```applescript
set TARGET_DEVICE_NAME to "iPad"
```

将 `"iPad"` 替换为你 iPad 的具体名称（例如 `"Leo 的 iPad Pro"`）。

---

## 📁 仓库结构

```
iPadAsMacMonitor/
├── Enable Sidecar.app.zip   ← v2.0（英文版）
├── 开启随航.app.zip          ← v2.0（中文版）
├── archive/
│   ├── Sidecar.app.zip      ← v1.0（英文版，已归档）
│   └── 开启随航.app.zip      ← v1.0（中文版，已归档）
├── README.md                ← 英文说明文档
└── README_zh.md             ← 本文件（中文说明文档）
```

---

## 📝 版本历史

| 版本 | 日期 | 说明 |
|---|---|---|
| v2.0 | 2026-02 | 重写以支持 macOS 26；模糊设备匹配；递归 UI 检测 |
| v1.0 | 2025-05 | 首次发布；基于硬编码菜单路径导航 |

---

## 🙏 反馈与贡献

如果 macOS 更新后脚本失效，欢迎提 Issue —— Apple 有时会在系统更新中调整无障碍树结构。

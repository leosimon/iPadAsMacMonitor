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

---

## 🔍 工作原理

这个 App 是一个 **Automator 应用**，内含一个"运行 AppleScript"动作。以下是你启动它之后，脚本逐步执行的完整流程。

### 执行流程

```
启动 App
    │
    ▼
① 播放"Pop"音效                       ← 提示脚本已开始执行
    │
    ▼
② 打开显示器设置                       ← 通过 URL Scheme 直接打开：
   x-apple.systempreferences:            无需导航菜单栏
   com.apple.preference.displays
    │
    ▼
③ 等待系统设置窗口出现                  ← 每 0.1 秒检测一次，最长等待 5 秒
    │
    ▼
④ 等待 1 秒                            ← 让显示器设置面板完全渲染完毕
    │
    ▼
⑤ 递归扫描无障碍树                     ← 最深搜索 4 层 UI 元素
   定位下拉按钮                           兼容 menu button / button / pop up button
    │
    ├─ 未找到 ─────────────────────────► 播放"Hero"音效（失败）→ 退出
    │
    ▼
⑥ 点击下拉按钮                         ← 打开"添加显示器"菜单
    │
    ▼
⑦ 等待菜单项加载                       ← 每 0.1 秒检测一次，最长等待 2 秒
    │
    ▼
⑧ 读取所有菜单项名称                   ← 依次尝试 title → name → AXTitle
    │
    ▼
⑨ 从末尾查找包含"iPad"的菜单项         ← 从后往前搜索：
                                          若同时开启了"通用控制"，
                                          同一台 iPad 会出现两次——
                                          随航（Sidecar）始终排在最后
    │
    ├─ 未找到 ─────────────────────────► 播放"Hero"音效（失败）→ 退出
    │
    ▼
⑩ 点击匹配的菜单项                     ← 发起随航连接
    │
    ▼
⑪ 播放"Blow"音效                       ← 连接成功启动
    │
    ▼
⑫ 等待 0.3 秒 → 退出系统设置          ← 恢复原始音量
```

### 辅助函数说明

| 函数名 | 作用 |
|---|---|
| `playSound(soundPath)` | 通过 `afplay` 命令播放系统音效 |
| `waitForApplicationWindow(appName, maxWait)` | 轮询等待指定 App 窗口出现，支持超时 |
| `openDisplaysSettings()` | 通过 URL Scheme 打开显示器设置，返回成功/失败 |
| `findMenuButtonOrDropdownButton(element, depth)` | 递归搜索无障碍树，查找任意类型的下拉按钮 |
| `waitForMenu(menuButton, maxWait)` | 轮询等待菜单至少加载出一个菜单项 |
| `findLastTargetIndexContains(text, list)` | 从列表末尾向前搜索，返回最后一个包含目标文字的索引 |
| `getMenuItemText(menuItem)` | 读取菜单项的显示文字，依次尝试 `title` → `name` → `AXTitle` |
| `findAndClickTargetDevice(menuButton)` | 综合以上函数，定位并点击正确的 iPad 菜单项 |
| `getFirstWindow()` | 获取系统设置的第一个窗口 |

### v2.0 为什么比 v1.0 更稳定

| 方面 | v1.0 方式 | v2.0 方式 |
|---|---|---|
| 打开设置 | 导航菜单栏：显示 → 显示器 | 直接使用 URL Scheme，不受菜单栏变化影响 |
| 定位按钮 | 硬编码 UI 路径（`group 1 of group 2 of splitter group 1 …`） | 递归扫描无障碍树，界面布局改变也能适配 |
| 设备匹配 | 精确字符串匹配（必须输入完整 iPad 名称） | 模糊 `contains` 匹配，名称包含"iPad"即可 |
| 重复项处理 | 未处理 | 从末尾搜索——随航始终优先于通用控制 |
| 错误处理 | 单一 `try/on error` 块 | 每个阶段都有独立的超时和降级处理 |

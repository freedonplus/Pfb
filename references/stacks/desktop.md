# 桌面端基线（Electron / Tauri）

## 选型推导

| 诉求 | 结论 |
| --- | --- |
| 生态成熟、Node 能力、兼容省心 | Electron（Chromium 版本固定，几乎无浏览器兼容问题；代价是包大 ~150M+） |
| 体积小（<20M）、内存低、Rust 侧能力 | Tauri 2（用系统 WebView → 必须做 WebView 兼容矩阵） |
| 老 Windows（Win7/无 WebView2）必须支持 | Electron（Tauri 在 Win7 基本不可用） |

## Electron 基线
- electron-vite + Vue/React + TS；主进程/预加载/渲染进程三层分离
- 安全红线：`nodeIntegration: false`、`contextIsolation: true`、远程内容禁用；ipcRenderer 白名单暴露
- 自动更新：electron-updater + 增量更新；签名（Win: signtool / macOS: notarize）
- 目录骨架：
```
├── electron/           # 主进程：main.ts、preload.ts、ipc/
├── src/                # 渲染层（同 Web 骨架）
├── build/              # 图标、安装包配置（electron-builder.yml）
└── release/            # 产出
```

## Tauri 基线
- Tauri 2 + Vite 前端（任意框架）+ Rust 后端命令
- 安全：CSP 配置、capability 权限最小化、禁用未用的 API
- 自动更新：内置 updater + 签名公钥
- WebView 兼容性矩阵必须实测：Win10/11 WebView2、macOS WKWebView、Linux WebKitGTK

## 通用项
- 安装包：Win（NSIS/MSI）、macOS（dmg，公证）、Linux（AppImage/deb）
- 崩溃收集：Sentry（主进程 + 渲染进程分开上报）
- 本地数据：SQLite / 文件存储路径规范（userData 目录）
- 单实例锁、托盘、全局快捷键为常见需求，提前确认
- 离线可用性策略：无网时本地缓存兜底与提示

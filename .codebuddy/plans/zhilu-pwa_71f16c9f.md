---
name: zhilu-pwa
overview: 为知录添加 PWA 支持，使其可以"安装到桌面"、离线可用
todos:
  - id: create-icons
    content: 生成 PWA 图标文件（192x192 和 512x512）
    status: completed
  - id: create-manifest
    content: 创建 manifest.json 并关联图标
    status: completed
    dependencies:
      - create-icons
  - id: create-sw
    content: 创建 sw.js Service Worker（缓存策略+版本管理）
    status: completed
  - id: modify-html
    content: 修改 zhilu.html 添加 PWA meta 标签和 SW 注册代码
    status: completed
    dependencies:
      - create-manifest
      - create-sw
  - id: test-pwa
    content: 启动本地服务验证 PWA 安装提示和离线缓存
    status: completed
    dependencies:
      - modify-html
---

## 产品概述

将知录（zhilu.html）从普通网页升级为 PWA，支持手机浏览器"添加到主屏幕"，安装后体验接近原生APP（全屏、无浏览器地址栏、独立图标）。

## 核心功能

- 创建 manifest.json 声明应用元信息（名称、图标、主题色、启动URL）
- 创建 Service Worker 实现核心资源离线缓存
- 修改 zhilu.html 添加 PWA 所需的 meta 标签和 SW 注册代码
- 生成 PWA 图标（192x192 和 512x512）

## 技术栈

- PWA 标准：Web App Manifest + Service Worker
- 缓存策略：Cache First（HTML/JS/CSS）+ Network First（Google Fonts）
- 图标：SVG 转 PNG（用 Canvas API 在 HTML 内生成，无需外部工具）

## 实现方案

### 1. manifest.json

- `name`: "知录 · 笔记"
- `short_name`: "知录"
- `start_url`: "./zhilu.html"
- `display`: "standalone"（全屏，无浏览器UI）
- `theme_color` / `background_color`: 与现有 zhilu.html 的 `--sidebar:#1a1f2e` 和 `--bg:#f5f3ef` 一致
- `icons`: 192x192 + 512x512

### 2. Service Worker (sw.js)

- 缓存策略：
- `zhilu.html` + `manifest.json` + 图标：Cache First，版本化缓存名
- Google Fonts CSS：Network First，失败回退缓存
- 安装时预缓存核心文件
- 激活时清理旧版本缓存
- fetch 事件拦截请求，按策略响应

### 3. zhilu.html 修改

- `<head>` 内添加：`<link rel="manifest">`、apple-mobile-web-app 系列 meta、主题色 meta
- `<body>` 末尾添加 SW 注册代码
- 图标通过内联 SVG + Canvas 生成后导出为 PNG data URL，或直接创建 SVG 图标文件

### 4. 图标生成

- 用 SVG 创建简约图标：深色背景 + "知" 字或笔记本图标
- 提供 192x192 和 512x512 两个尺寸
- SVG 可直接被 manifest 引用（部分浏览器支持），同时用 Canvas 转 PNG 兼容

## 目录结构

```
d:\AI-PM\zhilu-note\
├── zhilu.html          # [MODIFY] 添加 PWA meta + SW 注册
├── manifest.json       # [NEW] PWA 清单文件
├── sw.js               # [NEW] Service Worker 缓存策略
├── icons/              # [NEW] PWA 图标目录
│   ├── icon-192.png    # [NEW] 192x192 图标
│   └── icon-512.png    # [NEW] 512x512 图标
├── ai-note-output/     # 不变
└── scripts/            # 不变
```

## 实现注意事项

- Service Worker 仅在 HTTPS 或 localhost 下生效，部署时需确保 HTTPS
- 缓存版本号硬编码在 sw.js 中，更新资源时需同步修改
- `display: standalone` 使应用全屏运行，需确保 zhilu.html 自身有返回/导航逻辑（当前已有侧边栏）
- Google Fonts 离线时无法加载，已有 fallback 字体（-apple-system, PingFang SC 等），不影响可用性
- 图标用 Python + Pillow 或纯 HTML Canvas 生成；若无 Pillow 则用 SVG 文件
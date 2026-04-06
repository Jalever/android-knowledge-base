---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Android 四大组件基础、权限机制、网络编程基础
关联模块: 系统稳定性、性能优化
---

# WiFi 连接稳定性概要

## 核心原理

Android WiFi 框架采用分层架构，从应用层到硬件层依次为：

```
Application（应用层）
    ↓  WifiManager / ConnectivityManager API
Framework — WifiService（系统服务层）
    ↓  Binder IPC
wpa_supplicant（用户空间守护进程，负责 802.11 认证与关联）
    ↓  nl80211 / wext
WiFi Driver（内核驱动层）
    ↓
WiFi Hardware（硬件芯片）
```

### WifiManager vs ConnectivityManager 职责分工

| 维度 | WifiManager | ConnectivityManager |
|------|-------------|---------------------|
| 职责 | WiFi 专属操作：扫描、连接、断开、获取 WiFi 信息 | 全局网络管理：网络切换、网络可用性判断、NetworkCallback |
| 典型 API | `getScanResults()`、`getConnectionInfo()` | `registerNetworkCallback()`、`getActiveNetwork()` |
| 使用场景 | 需要直接操控 WiFi 的场景 | 监听网络状态变化、判断网络可达性 |
| 趋势 | Android 10+ 大量 API 被弃用/限制 | 推荐作为主要的网络状态感知方式 |

### NetworkCallback 机制（Android 5.0+）

Android 5.0 引入 `ConnectivityManager.NetworkCallback`，取代旧的 `CONNECTIVITY_ACTION` 广播，成为监听网络状态的推荐方式：

- **实时回调**：`onAvailable()` / `onLost()` / `onCapabilitiesChanged()` 等
- **精准过滤**：通过 `NetworkRequest` 指定关注的网络类型和能力
- **生命周期友好**：手动注册/注销，避免广播泄漏

## 发展趋势与版本演进

| Android 版本 | 关键变化 |
|-------------|---------|
| 5.0 (API 21) | 引入 `NetworkCallback`，开始推荐替代 `CONNECTIVITY_ACTION` |
| 8.0 (API 26) | 后台应用无法接收 `CONNECTIVITY_ACTION` 隐式广播 |
| 9.0 (API 28) | WiFi RTT（Round-Trip-Time）室内定位 API；前台扫描限制 4 次/2 分钟 |
| 10 (API 29) | **重大变更**：`WifiManager.enableNetwork()` 等连接 API 弃用；新增 `WifiNetworkSpecifier`（临时连接 IoT 设备）和 `WifiNetworkSuggestion`（建议系统连接） |
| 11 (API 30) | `WifiNetworkSuggestion` 增强、限制随机 MAC 行为 |
| 12 (API 31) | 附近设备权限 `NEARBY_WIFI_DEVICES`（替代部分位置权限需求） |
| 13+ (API 33+) | 持续收紧后台网络访问、推荐声明精确权限 |

## 主流工具与诊断方案

| 工具/命令 | 用途 |
|-----------|------|
| `adb shell dumpsys wifi` | 查看 WiFi 完整状态、连接历史、评分信息 |
| `adb shell dumpsys connectivity` | 查看网络连接全局状态 |
| `adb logcat -s WifiStateMachine` | 跟踪 WiFi 状态机转换日志 |
| `adb logcat -s WifiConnectivityManager` | 跟踪连接管理器决策日志 |
| `adb logcat -s wpa_supplicant` | 跟踪 wpa_supplicant 认证过程 |
| `adb shell cmd wifi status` | 快速查看当前 WiFi 状态（Android 11+） |
| Wireshark + tcpdump | 抓包分析 802.11 帧和 DHCP 流程 |

## 快速上手路径

建议按以下顺序阅读本模块文档：

1. **本文**（`00-overview.md`）— 建立 WiFi 连接管理的全局认知
2. [`01-WiFi连接管理wifi-connection-management.md`](01-WiFi连接管理wifi-connection-management.md) — WiFi 扫描、连接 API、状态监听的完整指南
3. [`02-断连排查与重连wifi-troubleshooting.md`](02-断连排查与重连wifi-troubleshooting.md) — 断连原因分析、日志排查方法与自动重连策略

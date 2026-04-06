---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: Android 四大组件生命周期、进程优先级、Kotlin 协程基础
关联模块: 05-系统稳定性system-stability/05-Crash采集与上报crash-collection-and-reporting, 05-系统稳定性system-stability/06-Watchdog与系统级监控watchdog-and-system-monitoring
---

# 异常恢复与容错设计

## 崩溃自动重启策略

### Application 级重启

> TODO: 通过 UncaughtExceptionHandler 重启到 LauncherActivity、清理 Task 栈、传递崩溃标志。

### Activity 级重启

> TODO: 系统自动恢复（SavedInstanceState）、自定义恢复逻辑。

### Service 重启策略

> TODO: START_STICKY / START_REDELIVER_INTENT / START_NOT_STICKY 对比与选型。

## 系统级重启方案

### PowerManager.reboot()

> TODO: 系统签名权限要求、reason 参数含义（recovery / bootloader / null）。

### Zygote 软重启

> TODO: SystemServer 崩溃 → Zygote 退出 → init 重启 Zygote 的完整流程。

### Shell 命令重启

> TODO: su -c reboot、需要 root 权限、适用场景。

### 定时重启策略

> TODO: AlarmManager 定时重启、凌晨执行、屏幕状态检查、适用于长运行设备。

### 方案对比

> TODO: 各重启方案的权限要求、适用场景、重启类型对比表。

## 状态持久化与恢复

### SavedInstanceState 机制

> TODO: onSaveInstanceState / onRestoreInstanceState 的触发时机与限制。

### 持久化存储恢复

> TODO: SharedPreferences / DataStore / 数据库保存关键业务状态、崩溃重启后读取恢复。

### ViewModel 与进程死亡

> TODO: ViewModel 不能在进程死亡后存活、SavedStateHandle 的作用。

## 优雅降级设计

### 功能降级

> TODO: 非核心功能异常时自动降级（如：推荐算法降级为默认列表）、Feature Flag 控制。

### 数据降级

> TODO: 网络不可用时使用缓存数据、数据库损坏时重建。

### UI 降级

> TODO: 组件加载失败时显示兜底 UI、ErrorBoundary 思想在 Android 中的应用。

## 进程保活方案

### 前台服务

> TODO: 优先级提升、常驻通知栏、适用场景（音乐、导航、下载）。

### WorkManager / JobScheduler

> TODO: 系统调度执行、不保证实时性、适用于后台任务。

### 厂商推送通道

> TODO: 借助厂商推送拉起、依赖厂商合作。

### 双进程守护（不推荐）

> TODO: 原理、为何在高版本 Android 不可靠、系统对抗方案的弊端。

### 方案对比与选型

> TODO: 各方案的可靠性、系统友好度、适用版本、推荐场景对比表。

## Safe Mode 安全模式设计

### 连续崩溃检测

> TODO: 记录崩溃次数与时间、短时间内多次崩溃触发 Safe Mode。

### Safe Mode 行为

> TODO: 禁用非核心功能 / 插件、使用默认配置启动、提示用户。

### 崩溃计数器重置

> TODO: 正常运行一段时间后清除崩溃计数。

## 崩溃防循环机制

> TODO: 崩溃 → 重启 → 再崩溃的死循环检测、达到阈值后停止重启并上报。

## IoT / 车机等长运行设备的特殊策略

> TODO: 定时重启释放资源、远程指令重启、OTA 回滚、硬件看门狗配合。

## 常见坑点

> TODO: 补充实际开发中的典型问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

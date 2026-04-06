---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: 性能优化概要（00-概要overview.md）、Android 后台任务机制、WorkManager 基础
关联模块: 05-网络性能优化network-performance-optimization
---

# 功耗优化

## Android 电量管理机制

### Doze 模式

> TODO: Doze 触发条件、两阶段行为（Light Doze / Deep Doze）、Maintenance Window

### App Standby Buckets

> TODO: Active / Working Set / Frequent / Rare / Restricted 五档分级，对后台任务的影响

### 后台执行限制演进

> TODO: Android 8.0+ 后台服务限制、Android 12+ 前台服务启动限制

### 电池优化白名单

> TODO: REQUEST_IGNORE_BATTERY_OPTIMIZATIONS 的使用与审慎

## 功耗分析工具

### Battery Historian

> TODO: bugreport 采集、Battery Historian 安装与使用、Wakelock/Job/Alarm 时间线分析

### Android Studio Energy Profiler

> TODO: 实时能耗监控、CPU/Network/GPS 能耗分布

### dumpsys batterystats

> TODO: 关键命令与输出字段解读、按 UID 查看应用耗电明细

### PowerManager API

> TODO: 应用内电量信息获取、BatteryManager 电量监听

## CPU 功耗优化

### 避免不必要的后台 CPU 占用

> TODO: 空转循环、过高频率的定时任务、后台动画未停止

### 计算任务调度优化

> TODO: 大量计算移至充电时执行、批量处理替代频繁小任务

## 网络功耗优化

### 批量网络请求

> TODO: 将多个小请求合并，减少无线模块唤醒次数

### 网络请求时机选择

> TODO: 利用 JobScheduler / WorkManager 在 WiFi/充电时执行非紧急网络请求

### 推送优化

> TODO: 使用 FCM/厂商推送替代长连接轮询，避免维持常驻连接

## WakeLock 管理

### WakeLock 类型与使用

> TODO: PARTIAL_WAKE_LOCK / SCREEN_DIM_WAKE_LOCK 等类型说明

### WakeLock 泄漏检测

> TODO: 未释放的 WakeLock 导致设备无法休眠，Battery Historian 中的排查方法

### WakeLock 最佳实践

> TODO: 使用 try-finally 确保释放、设置超时、用 WorkManager 替代手动 WakeLock

## GPS 与传感器功耗优化

### 定位精度与功耗平衡

> TODO: GPS / Network / Passive Provider 功耗对比、Fused Location Provider 使用

### 定位频率控制

> TODO: 根据业务场景动态调整定位间隔、Geofencing 替代持续定位

### 传感器管理

> TODO: 及时注销传感器监听、选择合适的采样率、批量传感器事件（SensorManager.SENSOR_DELAY）

## 后台任务优化

### WorkManager 最佳实践

> TODO: 约束条件设置（网络/充电/空闲）、定期任务 vs 一次性任务、链式任务

### Foreground Service 优化

> TODO: 前台服务的合理使用场景、短时前台服务（Android 14+限制）

### AlarmManager 优化

> TODO: setExactAndAllowWhileIdle 的节制使用、setInexactRepeating 批量对齐

### JobScheduler 调度策略

> TODO: Job 合并、延迟容忍度设置、与 WorkManager 的关系

## 常见坑点

### 1. 后台 Service 持有 WakeLock 不释放

> TODO: 导致设备无法进入 Doze，电量快速消耗

### 2. 频繁的位置更新请求

> TODO: GPS 持续工作导致电量剧增，用户投诉耗电

### 3. 动画/定时器未在 onPause 中停止

> TODO: Activity 不可见时仍在执行动画或定时刷新

### 4. 第三方 SDK 的隐式功耗

> TODO: 推送 SDK、统计 SDK 的后台唤醒行为排查

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、开源项目、优质博客链接

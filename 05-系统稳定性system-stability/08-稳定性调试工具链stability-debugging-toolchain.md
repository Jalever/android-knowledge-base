---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: ADB 基本操作、Android Studio 基本使用
关联模块: 05-系统稳定性system-stability/02-ANR深度解析anr-deep-analysis, 05-系统稳定性system-stability/04-NativeCrash与Tombstone分析native-crash-and-tombstone, 07-性能优化performance-optimization
---

# 稳定性调试工具链

## StrictMode

### 线程策略（ThreadPolicy）

> TODO: detectDiskReads / detectDiskWrites / detectNetwork / detectCustomSlowCalls 各检测项与配置。

### VM 策略（VmPolicy）

> TODO: detectLeakedSqlLiteObjects / detectLeakedClosableObjects / detectActivityLeaks 等检测项。

### 违规处理方式

> TODO: penaltyLog / penaltyFlashScreen / penaltyDeath / penaltyDropBox 各策略的适用场景。

### 日志解读

> TODO: StrictMode 违规日志的格式、duration 含义、定位到具体代码行。

## Android Profiler

### CPU Profiler

> TODO: 方法追踪录制、火焰图分析热点方法、线程状态查看。

### Memory Profiler

> TODO: 实时堆内存监控、Heap Dump 操作、泄漏的 Activity / Fragment 搜索。

### Network Profiler

> TODO: 网络请求时序图、请求 / 响应详情查看。

### Energy Profiler

> TODO: WakeLock 检测、JobScheduler 监控、异常唤醒识别。

## Perfetto

### 系统级 Trace 录制

> TODO: perfetto 命令行配置、ftrace_events 与 atrace_categories 选择、录制时长与缓冲区配置。

### Perfetto UI 分析

> TODO: ui.perfetto.dev 导入 trace、进程与线程时间线查看、主线程状态分析。

### ANR 场景分析

> TODO: 在 Perfetto 中定位 ANR 时间段、查看主线程状态（Running / Sleeping / Blocked / Uninterruptible Sleep）。

### 自定义 Trace 标记

> TODO: android.os.Trace API（beginSection / endSection）、Kotlin 扩展函数封装。

## adb bugreport

### 生成与解压

> TODO: adb bugreport 命令、zip 内容结构（主报告、ANR traces、tombstones、proto）。

### 关键段落解读

> TODO: "ANR in"、"CPU usage"、"iowait"、"100%TOTAL" 等关键字搜索与分析。

### 与 Battery Historian 配合

> TODO: bugreport 导入 Battery Historian 分析耗电与唤醒异常。

## Logcat

### 高效过滤技巧

> TODO: 按优先级 / Tag / PID / 时间范围过滤、正则匹配、事件日志（-b events）。

### 常用 Tag 速查表

> TODO: ActivityManager / WindowManager / DEBUG / lowmemorykiller / Watchdog / StrictMode 等。

### 规范化日志输出

> TODO: 统一 Tag、线程名标注、性能计时日志封装。

## Android Studio 内存分析器

### Heap Dump 分析

> TODO: 生成 Heap Dump、引用链追踪、Dominator Tree 与 Retained Size。

### Allocation Tracking

> TODO: 内存分配录制、热点分配调用栈定位。

## Simpleperf

> TODO: CPU 性能分析、采样模式与录制、FlameGraph 生成、与 Perfetto 的对比。

## MAT（Memory Analyzer Tool）

> TODO: 导入 HPROF 文件、Leak Suspects Report、Histogram 与 Dominator Tree、OQL 查询。

## 常见坑点

> TODO: 补充实际开发中的典型工具使用问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

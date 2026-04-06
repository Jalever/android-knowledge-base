---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: Java 异常处理、Android 多进程基础、网络请求基础
关联模块: 05-系统稳定性system-stability/01-Crash基础与分类crash-fundamentals, 05-系统稳定性system-stability/09-线上稳定性治理online-stability-governance
---

# Crash 采集与上报

## 自定义 UncaughtExceptionHandler

### 完整实现

> TODO: GlobalCrashHandler 的注册、崩溃信息收集（堆栈、设备信息、内存信息）、本地持久化。

### 链式调用与默认 Handler

> TODO: 保存并调用系统默认 Handler 的重要性、避免覆盖第三方 SDK 的 Handler。

### 多进程场景

> TODO: 每个进程都需初始化 CrashHandler、按进程名区分日志文件。

### 防递归崩溃

> TODO: isHandling 标志位、try-catch 保护、防止 CrashHandler 中再次崩溃导致无限循环。

## Native Crash 采集原理

### 信号处理 + fork 子进程 dump

> TODO: 注册 sigaction → 崩溃触发 → fork 子进程 → ptrace 读取寄存器与栈 → 生成 minidump。

### Breakpad 方案

> TODO: Google Breakpad 的架构、minidump 格式、minidump_stackwalk 解析、接入步骤。

### xCrash 方案

> TODO: 爱奇艺 xCrash 的架构特点（轻量、支持 Java + Native + ANR）、接入示例。

## ANR 采集方案

### FileObserver 监听

> TODO: 监听 /data/anr/ 目录、读取 traces.txt、权限限制与兼容性。

### 主线程 Watchdog

> TODO: 基于 HandlerThread 的心跳检测方案、超时阈值、主线程堆栈抓取。

### ApplicationExitInfo（Android 11+）

> TODO: 通过 REASON_ANR 获取 ANR traces、与传统方案的互补使用。

## 商业 / 开源方案接入

### Firebase Crashlytics

> TODO: 接入步骤、自动符号化、实时告警配置、与 Google Play Console 集成。

### Bugly（腾讯）

> TODO: 接入步骤、国内网络优势、符号表上传、多渠道配置。

### Sentry

> TODO: 接入步骤、私有化部署方案、Performance Monitoring 集成。

### 方案对比与选型

> TODO: 各方案在 Java Crash / Native Crash / ANR 支持度、部署方式、成本、生态等维度的对比表。

## 符号化流水线搭建

### CI 自动上传符号表

> TODO: 构建产物中提取未 strip .so、mapping.txt 的 CI 脚本、自动上传到 Crashlytics / Sentry。

### 版本 → 符号表映射管理

> TODO: 按 versionCode + Git commit hash 归档、存储策略、过期清理。

## 日志上报策略

### 本地持久化

> TODO: 崩溃日志写入文件、目录管理、容量限制。

### 优先级队列

> TODO: Crash > ANR > 性能数据的上报优先级设计。

### 网络上报与重试

> TODO: WorkManager 后台上报、指数退避重试、压缩传输。

## 常见坑点

> TODO: 补充实际开发中的典型问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

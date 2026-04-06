---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: Android 系统启动流程（Zygote / SystemServer）、Linux 内核基础
关联模块: 05-系统稳定性system-stability/01-Crash基础与分类crash-fundamentals, 05-系统稳定性system-stability/07-异常恢复与容错设计exception-recovery-and-fault-tolerance
---

# Watchdog 与系统级监控

## Android Framework Watchdog

### Watchdog 源码解析

> TODO: Watchdog 线程的启动时机、HandlerChecker 与 MonitorChecker 双重检测机制。

### 监控的关键服务

> TODO: AMS、WMS、PMS、DisplayThread、UiThread、IoThread、AnimationThread 等监控对象列表。

### 检测流程

> TODO: 发送检测消息 → 30s 半超时 → 60s 全超时 → dump 系统状态 → 杀死 SystemServer 的完整流程图。

### 系统状态 Dump 内容

> TODO: Watchdog 触发时收集的信息（线程堆栈、kernel 日志、blocked 进程列表）。

## Kernel Watchdog

### 软件看门狗（softlockup）

> TODO: 内核线程定期检测 CPU 调度、超时行为（告警日志）、配置参数。

### 硬件看门狗（hardlockup）

> TODO: NMI 中断检测 CPU 锁死、触发 Kernel Panic → 硬重启。

### softlockup vs hardlockup 对比

> TODO: 检测机制、超时行为、适用场景对比表。

## 硬件看门狗芯片

### 工作原理

> TODO: 独立芯片、系统定期"喂狗"、未喂狗时切断 / 重置电源。

### 喂狗机制实现

> TODO: 内核驱动定期写入喂狗寄存器、用户态喂狗方案。

### 适用场景

> TODO: IoT 设备、工控主板、车机系统等长运行场景。

## 自定义应用级 Watchdog

### 主线程 Watchdog 实现

> TODO: 基于 HandlerThread 的心跳检测、超时回调、主线程堆栈抓取。

### 业务线程 Watchdog

> TODO: 监控关键业务线程（网络线程、数据库线程等）的健康状态。

### Watchdog 与 ANR 检测的关系

> TODO: 两者的异同、互补使用策略。

## DropBoxManager

### 系统日志收集机制

> TODO: DropBoxManager 的定位、存储的日志类型（crash / wtf / anr / tombstone）。

### 日志读取与分析

> TODO: adb shell dumpsys dropbox、按 Tag 过滤、时间范围查询。

### 在应用中读取 DropBox 日志

> TODO: DropBoxManager API 使用、权限要求（READ_LOGS）。

## 常见坑点

> TODO: 补充实际开发中的典型问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

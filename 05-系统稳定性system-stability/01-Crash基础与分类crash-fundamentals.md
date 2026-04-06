---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: Java 异常体系、Android 进程模型、Linux 信号基础
关联模块: 05-系统稳定性system-stability/00-概要overview
---

# Crash 基础与分类

## Java Crash

### RuntimeException 体系与常见类型

> TODO: NPE、OOM、ClassNotFoundException、IllegalStateException 等分类与典型场景。

### UncaughtExceptionHandler 原理

> TODO: Thread.setDefaultUncaughtExceptionHandler 调用链、系统默认处理流程。

### 堆栈解读方法

> TODO: Logcat 中 FATAL EXCEPTION 日志结构、关键字段解读、多线程堆栈分析。

## Native Crash

### Linux 信号机制概述

> TODO: 信号（Signal）基本概念、常见崩溃信号分类表（SIGSEGV / SIGABRT / SIGBUS / SIGFPE / SIGILL）。

### debuggerd 工作流程

> TODO: 崩溃发生 → debuggerd 捕获 → 生成 Tombstone 的完整流程。

### Native Crash 与 Java Crash 的区别

> TODO: 对比表（触发方式、日志位置、捕获手段、分析工具）。

## System Server Crash

### SystemServer 角色与崩溃影响

> TODO: SystemServer 承载的关键服务、崩溃传导链（SystemServer → Zygote → init → 软重启）。

### 常见触发原因

> TODO: 系统服务死锁、资源耗尽、Framework Bug 等。

## Kernel Panic

### 内核不可恢复错误

> TODO: Kernel Panic 的定义、与 Oops 的区别、常见原因（驱动异常、内存损坏、硬件故障）。

### 日志恢复机制

> TODO: last_kmsg、pstore / ramoops 机制、如何从硬重启中提取内核日志。

## ApplicationExitInfo（Android 11+）

### API 概述

> TODO: ApplicationExitInfo 的定位、支持的退出原因枚举。

### 获取退出原因示例

> TODO: getHistoricalProcessExitReasons 用法、按 reason 过滤（REASON_CRASH / REASON_ANR / REASON_LOW_MEMORY 等）。

### 与传统方案的对比

> TODO: 对比 UncaughtExceptionHandler、Tombstone 等传统方案的优劣。

## 常见坑点

> TODO: 补充实际开发中的典型问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

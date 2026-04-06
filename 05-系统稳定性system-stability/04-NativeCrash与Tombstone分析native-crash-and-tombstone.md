---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: C/C++ 基础、Linux 信号机制、NDK 开发基础
关联模块: 05-系统稳定性system-stability/01-Crash基础与分类crash-fundamentals, 05-系统稳定性system-stability/05-Crash采集与上报crash-collection-and-reporting
---

# Native Crash 与 Tombstone 分析

## Linux 信号机制详解

### 信号基本概念

> TODO: 信号的定义、同步信号与异步信号、信号处理流程（内核 → 用户态 handler）。

### 常见崩溃信号分类

> TODO: SIGSEGV / SIGABRT / SIGBUS / SIGFPE / SIGILL 的触发条件、子码含义、典型场景对照表。

### 自定义信号处理器

> TODO: sigaction 注册、信号处理函数中的安全操作（async-signal-safe）、备用栈（sigaltstack）。

## Tombstone 文件完整结构

### 头部信息

> TODO: Build fingerprint、ABI、Timestamp、进程 / 线程信息、信号与 fault addr。

### 寄存器快照

> TODO: ARM64 寄存器（x0-x30 / sp / pc / lr）的含义与分析方法。

### 调用栈（backtrace）

> TODO: pc 偏移量解读、.so 文件定位、帧编号与调用顺序。

### 内存映射（memory map）

> TODO: /proc/[pid]/maps 对应、如何判断地址属于哪个库、栈 / 堆 / mmap 区域识别。

### 日志段（logcat / log）

> TODO: Tombstone 中附带的 logcat 片段、abort message 字段。

## 符号化还原

### addr2line

> TODO: llvm-addr2line 的用法、参数说明、单地址解析示例。

### ndk-stack

> TODO: 批量解析完整 tombstone、从 logcat 管道输入的用法。

### llvm-symbolizer

> TODO: 与 addr2line 的区别、批量处理优势。

### 符号表归档与 CI 集成

> TODO: 未 strip .so 文件的保留策略、CI/CD 自动归档流程、版本 → 符号表映射。

## 内存安全检测工具

### GWP-ASan

> TODO: 低开销生产环境可用、AndroidManifest 启用方式、检测能力（use-after-free / buffer-overflow）。

### HWASan（Hardware Address Sanitizer）

> TODO: 硬件辅助检测、适用场景、性能开销、与 ASan 的区别。

### ASan（Address Sanitizer）

> TODO: 开发期全量检测、编译配置、典型输出解读。

## JNI 异常处理最佳实践

### ExceptionCheck 模式

> TODO: 每次 JNI 调用后检查异常、ExceptionClear + return 模式。

### 常见 JNI 崩溃模式

> TODO: 未检查异常导致 SIGABRT、JNI 引用泄漏、线程附着（AttachCurrentThread）遗漏。

### JNI 调试技巧

> TODO: CheckJNI 模式启用、-Xcheck:jni 参数、JNI 日志 Tag。

## 常见 Native Crash 模式与修复

> TODO: 空指针解引用、野指针 / use-after-free、栈溢出、双重释放、对齐错误的识别与修复策略表。

## 常见坑点

> TODO: 补充实际开发中的典型 Native 崩溃问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

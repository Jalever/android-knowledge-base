---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: Java 内存管理与 GC 基础、Android 进程优先级
关联模块: 05-系统稳定性system-stability/00-概要overview, 07-性能优化performance-optimization/01-内存优化memory-optimization
---

# 内存稳定性与 OOM

## Android 进程内存模型

### 内存区域划分

> TODO: Java Heap / Native Heap / mmap / Stack / Code 各区域的用途与限制。

### 进程内存上限

> TODO: dalvik.vm.heapsize / heapgrowthlimit 配置、不同设备的典型值、largeHeap 选项。

## Low Memory Killer（lmkd）

### OOM Adj 优先级体系

> TODO: OOM Adj 值与进程优先级对照表（FOREGROUND / VISIBLE / SERVICE / CACHED 等）。

### lmkd 工作机制

> TODO: 内存阈值（minfree）配置、进程回收顺序、与旧版 LMK 驱动的区别。

### 进程优先级提升策略

> TODO: 如何合理提升进程优先级以减少被回收概率。

## OOM 类型分类

### Java OOM

> TODO: Java Heap 溢出、常见触发场景（大图加载、缓存过大、集合膨胀）。

### Native OOM

> TODO: Native Heap 溢出、mmap 失败、虚拟地址空间耗尽（32 位进程）。

### FD 耗尽

> TODO: 文件描述符泄漏导致的 "Too many open files" 错误、常见泄漏源。

### 线程数限制

> TODO: pthread_create 失败、Android 线程数上限、线程泄漏排查。

## 内存泄漏常见模式

### Activity / Fragment 泄漏

> TODO: 常见引用链（静态变量 → Activity、单例 → Activity、内部类 → 外部类）。

### Handler 泄漏

> TODO: 匿名 Handler 持有 Activity 引用的经典问题、WeakReference 解法。

### 匿名内部类与 Lambda

> TODO: Kotlin Lambda 捕获外部引用导致的泄漏、解决方案。

### 单例持有 Context

> TODO: 使用 Application Context 替代 Activity Context 的原则。

### 资源未关闭

> TODO: Cursor、InputStream、TypedArray、BroadcastReceiver 等未释放。

## LeakCanary 使用与原理

### 接入与配置

> TODO: 依赖引入、自动检测模式、自定义配置。

### 检测原理

> TODO: ObjectWatcher + WeakReference + GC 触发 + Heap Dump 分析链路。

### 泄漏报告解读

> TODO: Leak Trace 中的引用链解读方法。

## onTrimMemory 回调处理

> TODO: 各级别含义（TRIM_MEMORY_RUNNING_LOW / TRIM_MEMORY_UI_HIDDEN 等）、推荐的资源释放策略。

## Bitmap 与大图内存管理

> TODO: Bitmap 内存计算、inSampleSize 降采样、Bitmap 复用池、Glide/Coil 的内存管理策略。

## 线程与 FD 泄漏排查

> TODO: /proc/[pid]/fd 检查、/proc/[pid]/status 中线程数监控、排查工具与脚本。

## 常见坑点

> TODO: 补充实际开发中的典型内存问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

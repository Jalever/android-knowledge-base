---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: 性能优化概要（00-概要overview.md）、Room/SQLite 基础、Kotlin 协程基础
关联模块: 09-多线程与协程性能threading-and-coroutine-performance
---

# IO 与存储性能

## Android 存储模型概述

### 内部存储 vs 外部存储

> TODO: 存储路径、访问权限、性能差异

### Scoped Storage（分区存储）

> TODO: Android 10+ 分区存储对 IO 性能的影响、MediaStore 访问开销

## SharedPreferences 性能问题

### SP 的工作原理

> TODO: 全量 XML 加载、commit vs apply 区别、ANR 风险（apply 在 Activity onPause 时同步等待）

### SP 的性能陷阱

> TODO: 单文件过大、频繁写入、多进程不安全

### MMKV 替代方案

> TODO: mmap 原理、MMKV 性能优势（读写速度对比）、迁移方式

### Jetpack DataStore

> TODO: Preferences DataStore vs Proto DataStore、协程 API、与 SP 性能对比

## SQLite / Room 数据库性能

### 数据库查询优化

> TODO: 索引设计、EXPLAIN QUERY PLAN 分析、避免全表扫描

### 批量操作优化

> TODO: 事务包裹批量插入、INSERT OR REPLACE 性能、WAL 模式

### Room 特有优化

> TODO: @RawQuery 避免动态查询开销、Paging 3 分页加载、Flow/LiveData 响应式查询

### 数据库体积管理

> TODO: 定期 VACUUM、合理设置 journal_size_limit、监控数据库文件大小

## 文件 IO 优化

### 缓冲 IO 与直接 IO

> TODO: BufferedInputStream/BufferedOutputStream 的缓冲区大小选择

### mmap 内存映射

> TODO: mmap 原理、适用场景（大文件随机读取）、在 Android 中的使用

### 异步文件操作

> TODO: 将文件读写移至 IO 线程、协程 withContext(Dispatchers.IO)

### 文件读写性能基准

> TODO: 不同存储介质（内部/外部/SD 卡）的读写速度差异

## ContentProvider 性能

### ContentProvider 查询开销

> TODO: 跨进程 Binder 调用开销、Cursor 数据拷贝

### ContentProvider 优化策略

> TODO: 批量操作 applyBatch、减少投影列、ContentProviderClient 复用

## 序列化方案性能对比

### JSON 解析性能

> TODO: Gson vs Moshi vs kotlinx.serialization 性能对比、流式解析 vs 树模型解析

### Protocol Buffers

> TODO: Protobuf 编解码性能、体积优势、在 Android 中的使用

### Parcelable vs Serializable

> TODO: 性能差异原因、@Parcelize 注解简化 Parcelable 实现

## 常见坑点

### 1. 主线程执行 SP commit 导致 ANR

> TODO: SP.commit() 是同步操作，大文件写入会阻塞主线程

### 2. 数据库未加索引导致查询缓慢

> TODO: 随数据量增长查询时间从毫秒级退化到秒级

### 3. Cursor 未关闭导致资源泄漏

> TODO: 数据库 Cursor / ContentProvider Cursor 使用后必须关闭

### 4. 大文件读取导致 OOM

> TODO: 一次性读取大文件到 byte[] 的内存风险，应使用流式读取

### 5. apply 在 Activity 停止时的隐式等待

> TODO: SharedPreferences.apply() 的 QueuedWork 机制，可能在 onPause/onStop 时阻塞

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、开源项目、优质博客链接

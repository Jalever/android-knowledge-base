---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: 性能优化概要（00-概要overview.md）、Kotlin 协程基础、Java 并发基础
关联模块: 04-卡顿检测与优化jank-detection-and-optimization
---

# 多线程与协程性能

## Android 线程模型

### 主线程（UI Thread）

> TODO: 主线程职责、消息循环机制、为什么不能执行耗时操作

### 常见线程类型

> TODO: 主线程、Binder 线程、RenderThread、后台工作线程的角色与关系

### 线程数量对性能的影响

> TODO: 过多线程的内存开销（每线程 ~1MB 栈）、上下文切换开销、线程数爆炸问题

## 线程池配置与调优

### Executors 内置线程池

> TODO: FixedThreadPool / CachedThreadPool / SingleThreadExecutor / ScheduledThreadPool 适用场景

### 自定义线程池参数

> TODO: corePoolSize / maximumPoolSize / keepAliveTime / workQueue / RejectedExecutionHandler 配置指南

### OkHttp / Retrofit 线程池

> TODO: 网络库内置线程池行为分析，避免重复创建线程池

### 线程池监控

> TODO: 活跃线程数、队列积压、任务执行耗时监控

## Kotlin 协程性能模式

### Dispatchers 选择

> TODO: Main / IO / Default / Unconfined 的底层实现与适用场景

### Structured Concurrency

> TODO: CoroutineScope 生命周期管理、避免协程泄漏

### 协程 vs 线程性能对比

> TODO: 协程的轻量优势、协程挂起/恢复的开销分析

### Flow 性能注意

> TODO: 冷流 vs 热流、conflate / debounce / buffer 操作符的性能影响、flowOn 切换调度器

### 协程异常处理与性能

> TODO: SupervisorScope、异常传播对兄弟协程的影响

## Handler / Looper 使用注意

### Handler 消息优先级

> TODO: 同步消息 vs 异步消息、sync barrier 对渲染的影响

### IdleHandler 的妙用

> TODO: 在主线程空闲时执行低优先级任务

### HandlerThread 使用场景

> TODO: 需要顺序执行后台任务时使用 HandlerThread 替代线程池

## 线程同步与锁优化

### synchronized vs ReentrantLock

> TODO: 性能对比、公平锁/非公平锁、tryLock 避免死锁

### 无锁并发方案

> TODO: AtomicXxx、ConcurrentHashMap、CopyOnWriteArrayList 的适用场景

### 读写锁（ReadWriteLock）

> TODO: 读多写少场景的性能优化

### 避免死锁

> TODO: 锁排序、超时获取、死锁检测方案

## 异步方案迁移

### AsyncTask 迁移

> TODO: AsyncTask 废弃原因、迁移到协程/Executor 的方案

### RxJava 性能注意点

> TODO: Schedulers 选择、避免过长操作符链、内存泄漏防范（CompositeDisposable）

### ListenableFuture / Guava

> TODO: Jetpack 中的 ListenableFuture 使用、与协程的桥接

## 线程数监控与治理

### 线程数统计

> TODO: /proc/self/status 中 Threads 字段、Debug.getThreadCount()

### 线程泄漏检测

> TODO: 线程未正确关闭导致线程数持续增长、KOOM 线程泄漏检测

### 线程命名规范

> TODO: 为所有线程/线程池设置可辨识名称，便于 Trace 分析和问题定位

## 常见坑点

### 1. GlobalScope 滥用导致协程泄漏

> TODO: GlobalScope 的生命周期是进程级，协程不会自动取消

### 2. Dispatchers.IO 默认线程数限制

> TODO: 默认最多 64 个线程，密集 IO 场景可能不够用

### 3. 线程池任务队列无界导致 OOM

> TODO: CachedThreadPool 的无界队列 + 大量任务 → 线程爆炸

### 4. withContext 频繁切换的开销

> TODO: 每次 withContext 都有调度开销，批量操作应包裹在一次 withContext 中

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、开源项目、优质博客链接

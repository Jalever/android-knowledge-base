---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Android 四大组件基础、Handler 消息机制、Linux 进程管理基础
关联模块: 07-性能优化
---

# 系统稳定性概要

## 核心原理

Android 系统稳定性保障是一套多层次的防御体系，从内核到应用层层设防，确保设备在异常情况下能够检测、恢复或优雅降级。

```mermaid
graph TB
    subgraph 应用层
        A[UncaughtExceptionHandler]
        B[ANR 检测]
    end
    subgraph Framework 层
        C[Watchdog]
        D[ActivityManagerService]
        E[Crash Handler - DropBoxManager]
    end
    subgraph Native 层
        F[Debuggerd / Tombstone]
        G[Signal Handler]
    end
    subgraph 内核层
        H[Low Memory Killer / lmkd]
        I[Kernel Watchdog]
        J[OOM Killer]
    end

    A --> D
    B --> D
    C --> D
    D --> E
    G --> F
    H --> J
```

### 四大核心机制

| 机制 | 层级 | 作用 | 触发条件 |
|------|------|------|----------|
| **Watchdog** | Framework | 监控系统关键服务是否死锁 | 关键锁 60s 内未释放 |
| **ANR 检测** | Framework | 检测应用主线程无响应 | Input 5s / Broadcast 10s / Service 20s |
| **Crash Handler** | 应用/Native | 捕获未处理异常和信号 | 未捕获异常、非法内存访问等 |
| **Low Memory Killer** | 内核 | 在内存不足时按优先级杀进程 | 可用内存低于阈值 |

## Crash 分类

### Java Crash

应用层未捕获的异常（`RuntimeException`、`OutOfMemoryError` 等），由虚拟机抛出，通过 `Thread.UncaughtExceptionHandler` 捕获。产生的日志可在 Logcat 中以 `FATAL EXCEPTION` 标识查看。

### Native Crash（Tombstone）

Native 层（C/C++ 代码）因信号触发的崩溃，常见信号包括 `SIGSEGV`（段错误）、`SIGABRT`（主动终止）、`SIGBUS`（总线错误）。崩溃信息由 `debuggerd` 生成 Tombstone 文件，保存于 `/data/tombstones/`。

### System Server Crash

SystemServer 进程崩溃会导致 Zygote 重启，进而引发整个用户空间重启（软重启）。Watchdog 检测到死锁时也会触发此流程。

### Kernel Panic

内核不可恢复的致命错误，导致设备硬重启。常见原因包括驱动异常、内存损坏、硬件故障等。日志需通过 `last_kmsg` 或 `pstore` 机制恢复。

## ANR 原理

ANR（Application Not Responding）的本质是 **主线程在规定时间内未完成预期工作**。

```mermaid
sequenceDiagram
    participant System as AMS / InputDispatcher
    participant Main as 应用主线程
    participant Handler as MessageQueue

    System->>Main: 发送事件（Input / Broadcast / Service）
    System->>System: 启动超时计时器
    Main->>Handler: 消息入队等待处理
    alt 主线程及时处理
        Handler->>Main: 处理完成
        Main->>System: 移除计时器
    else 主线程阻塞
        System->>System: 超时触发
        System->>System: 收集 traces.txt、CPU 使用率
        System->>Main: 弹出 ANR 对话框
    end
```

### ANR 超时阈值

| 场景 | 超时时间 | 说明 |
|------|----------|------|
| InputDispatching | 5 秒 | 触摸/按键事件未在 5s 内被消费 |
| BroadcastReceiver | 前台 10 秒 / 后台 60 秒 | `onReceive()` 执行超时 |
| Service | 前台 20 秒 / 后台 200 秒 | `onCreate()` / `onStartCommand()` 超时 |
| ContentProvider | 10 秒 | `publish` 超时（Android 12+） |

## 发展趋势

| Android 版本 | 稳定性相关变化 |
|-------------|---------------|
| Android 8.0 | 后台执行限制，减少后台 Crash 概率 |
| Android 10 | 引入 `malloc` 调试工具 HWASan |
| Android 11 | **GWP-ASan**：低开销的内存安全错误检测，可在生产环境启用 |
| Android 12 | **Restricted Standby Bucket**：限制异常应用的资源使用；改进 Tombstone 格式（protobuf） |
| Android 13 | 前台服务任务管理器，用户可直接停止异常服务 |
| Android 14 | 改进 `ApplicationExitInfo`，提供更详细的退出原因；增强 `onTrimMemory` 回调 |
| Android 15 | 增强 16KB 页面大小支持，影响 Native 内存管理与崩溃排查 |

### GWP-ASan 启用方式

在 `AndroidManifest.xml` 中为应用启用：

```xml
<application android:gwpAsanMode="always">
    ...
</application>
```

## 主流方案与开源项目对比

| 方案 | 类型 | Java Crash | Native Crash | ANR | 优势 | 劣势 |
|------|------|:---:|:---:|:---:|------|------|
| **Firebase Crashlytics** | 商业（免费） | ✅ | ✅ | ✅ | Google 生态集成好、实时告警 | 依赖 Google 服务 |
| **Bugly（腾讯）** | 商业（免费） | ✅ | ✅ | ✅ | 国内网络友好、符号化便捷 | 社区维护放缓 |
| **Sentry** | 开源/商业 | ✅ | ✅ | ✅ | 可私有部署、多平台支持 | 自建成本较高 |
| **xCrash（爱奇艺）** | 开源 | ✅ | ✅ | ✅ | 轻量、无需 root、支持 ANR 捕获 | 仅采集不含分析平台 |
| **ACRA** | 开源 | ✅ | ❌ | ❌ | 极轻量、可自定义上报后端 | 仅 Java Crash |

### 选型建议

- **国内项目 + 快速接入**：Bugly 或 xCrash
- **海外项目 + Google 生态**：Firebase Crashlytics
- **私有化部署需求**：Sentry 自建
- **需要极致轻量或定制**：xCrash + 自建分析后台

## 快速上手路径

```mermaid
graph LR
    A["1. 阅读本概要"] --> B["2. Crash 恢复机制"]
    B --> C["3. 稳定性调试方法论"]
    C --> D["4. 动手实践"]
    D --> E["5. 接入线上监控"]

    click B "01-crash-recovery.md"
    click C "02-stability-debugging.md"
```

**推荐阅读顺序：**

1. **本文**（00-overview）—— 建立系统稳定性全景认知
2. [死机检测与重启恢复](01-crash-recovery.md) —— 理解检测机制与恢复策略
3. [稳定性调试方法论](02-stability-debugging.md) —— 掌握排查工具链与实战技巧

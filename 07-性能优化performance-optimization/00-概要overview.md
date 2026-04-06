---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Android 四大组件基础、Activity/Fragment 生命周期、Kotlin 基础
关联模块: 系统稳定性
---

# 性能优化概要

## 模块定位

性能优化是 Android 应用用户体验的核心保障。启动慢、滑动卡、内存高、包太大、耗电快——任何一个维度的性能短板都会直接影响用户留存和口碑。本模块系统性地覆盖从内存、渲染、启动、网络、包体积、功耗，到 IO 存储、线程并发、Compose、监控体系的完整知识链条，帮助团队建立"能度量、能分析、能优化、能防劣化"的全方位性能工程能力。

| 领域 | 说明 | 对应文件 |
|------|------|----------|
| 内存优化 | 内存泄漏、抖动、OOM、Bitmap 优化 | `01-内存优化` |
| UI 渲染性能 | 帧率、过度绘制、布局优化、RecyclerView | `02-UI渲染性能` |
| 启动优化 | 冷/温/热启动、异步初始化、Baseline Profile | `03-启动优化` |
| 卡顿检测与优化 | 主线程卡顿、Looper 监控、帧率检测 | `04-卡顿检测与优化` |
| 网络性能优化 | DNS、连接复用、缓存、弱网、数据压缩 | `05-网络性能优化` |
| 包体积优化 | R8、资源压缩、SO 裁剪、动态下发 | `06-包体积优化` |
| 功耗优化 | Doze、WakeLock、后台任务、传感器管理 | `07-功耗优化` |
| IO 与存储性能 | SP/DataStore/MMKV、Room 优化、文件 IO | `08-IO与存储性能` |
| 多线程与协程性能 | 线程池调优、协程模式、锁优化 | `09-多线程与协程性能` |
| Compose 性能优化 | 重组优化、Stability、LazyLayout | `10-Compose性能优化` |
| 性能监控与 CI 集成 | APM 方案、Benchmark、CI 卡点、防劣化 | `11-性能监控与CI集成` |

## 知识全景图

```mermaid
mindmap
  root((性能优化模块))
    导航层
      00 概要
    基础层
      01 内存优化
        内存泄漏
        内存抖动
        OOM
        Bitmap 优化
      02 UI 渲染性能
        帧率与掉帧
        过度绘制
        布局层级
        RecyclerView
      03 启动优化
        冷启动/温启动/热启动
        异步初始化
        Baseline Profile
    核心层
      04 卡顿检测与优化
        Looper 监控
        BlockCanary
        Choreographer
        冻帧检测
      05 网络性能优化
        DNS 优化
        连接复用
        缓存策略
        弱网优化
      06 包体积优化
        R8 代码缩减
        资源压缩
        SO 裁剪
        动态下发
      07 功耗优化
        Doze 与 Standby
        WakeLock
        后台任务
        传感器
    场景层
      08 IO 与存储性能
        SP / DataStore / MMKV
        Room 数据库
        文件 IO
      09 多线程与协程
        线程池调优
        协程模式
        锁优化
      10 Compose 性能
        Recomposition
        Stability
        LazyLayout
    工程层
      11 性能监控与 CI
        APM 方案
        Benchmark
        CI 卡点
        防劣化
```

## 核心原理

Android 性能优化涵盖多个维度，构成一个完整的优化全景图：

| 维度 | 核心目标 | 用户感知 |
|------|---------|---------|
| 启动速度 | 缩短冷启动到首帧绘制的时间 | 点击图标到可交互的等待时长 |
| 渲染性能 | 稳定 60fps，避免掉帧 | 滑动流畅度、动画卡顿 |
| 内存管理 | 控制内存占用、消除泄漏 | 应用被杀概率、OOM 崩溃 |
| 卡顿 | 消除主线程阻塞与冻帧 | 操作响应速度、ANR 弹窗 |
| 网络优化 | 降低延迟、减少流量消耗 | 页面加载速度、流量费用 |
| 包体积 | 缩减 APK/AAB 体积 | 下载意愿、安装成功率 |
| 功耗优化 | 降低 CPU/GPS/网络唤醒频率 | 电量消耗速度 |
| IO/存储 | 减少磁盘 IO 耗时与阻塞 | 数据加载速度、SP 引起的卡顿 |
| 线程/并发 | 合理调度、避免线程爆炸 | 整体流畅性、后台资源消耗 |
| Compose | 减少不必要重组、优化列表 | Compose UI 流畅度 |

## 性能指标体系

| 指标 | 衡量标准 | 采集方式 |
|------|---------|---------|
| 启动时间 | 冷启动 < 2s，温启动 < 1s | `adb shell am start -W`、Macrobenchmark |
| 帧率/掉帧 | P95 帧耗时 < 16ms，冻帧率 < 0.1% | FrameMetrics API、Perfetto |
| 内存占用 | PSS 控制在设备内存 1/8 以内 | `adb shell dumpsys meminfo`、Memory Profiler |
| 内存泄漏 | Activity/Fragment 关闭后可被 GC 回收 | LeakCanary、MAT |
| CPU 使用率 | 空闲时 < 3%，交互时 < 30% | Android Profiler、`top` 命令 |
| 电量消耗 | 后台每小时耗电 < 1% | Battery Historian、`dumpsys batterystats` |

## 工具链总览

```mermaid
flowchart LR
    A[性能分析工具] --> B[综合型]
    A --> C[内存]
    A --> D[渲染]
    A --> E[启动/Trace]

    B --> B1[Android Studio Profiler]
    B --> B2[Perfetto]

    C --> C1[LeakCanary]
    C --> C2[MAT]
    C --> C3[Memory Profiler]

    D --> D1[GPU 过度绘制]
    D --> D2[Layout Inspector]
    D --> D3[GPU 渲染模式分析]

    E --> E1[Systrace]
    E --> E2[Macrobenchmark]
    E --> E3[Baseline Profile]
```

| 工具 | 用途 | 使用门槛 |
|------|------|---------|
| Android Studio Profiler | CPU/内存/网络/能耗综合分析 | 低 |
| Perfetto | 系统级 Trace 分析，替代 Systrace | 中 |
| Systrace | 传统系统跟踪工具 | 中 |
| MAT | 分析 hprof 文件，定位内存泄漏 | 高 |
| LeakCanary | 自动检测 Activity/Fragment 内存泄漏 | 低 |
| Macrobenchmark | 自动化启动/滑动性能测试 | 中 |

## 发展趋势

- **Baseline Profile**：通过预编译热路径代码，冷启动速度提升 30%+，Google Play 已自动收集生成
- **Macrobenchmark / Microbenchmark**：Jetpack 官方性能测试框架，支持 CI 集成，取代手动测量
- **Jetpack Compose 性能**：Compose 编译器优化（Stability、Strong Skipping Mode）、`LazyColumn` 替代 RecyclerView、`derivedStateOf` 减少重组
- **App Startup 库**：统一管理 ContentProvider 初始化，消除启动阶段的隐式耗时
- **R8 全模式（Full Mode）**：更激进的代码缩减与优化，减小包体积

## 主流方案与开源项目

| 项目 | 来源 | 聚焦领域 | 特点 |
|------|------|---------|------|
| [LeakCanary](https://github.com/square/leakcanary) | Square | 内存泄漏 | 零配置自动检测，开发阶段必备 |
| [KOOM](https://github.com/nicklfy/KOOM) | 快手 | 内存/线程泄漏 | 线上监控，低开销 hprof dump |
| [Matrix](https://github.com/nicklfy/matrix) | 微信 | 综合性能 | 涵盖卡顿、内存、IO、电量等 |
| [Booster](https://github.com/nicklfy/booster) | 滴滴 | 构建期优化 | 编译插桩、资源压缩、多线程优化 |
| [Anchors](https://github.com/nicklfy/Anchors) | 社区 | 启动框架 | 有向无环图调度初始化任务 |
| [BlockCanary](https://github.com/nicklfy/BlockCanary) | 社区 | 卡顿检测 | 基于 Looper Printer 检测主线程卡顿 |

## 推荐阅读路径

### 新人入门路径

适合刚接触 Android 性能优化的开发者，按顺序阅读：

```mermaid
graph LR
    A["00 概要"] --> B["01 内存优化"]
    B --> C["02 UI 渲染"]
    C --> D["03 启动优化"]
    D --> E["04 卡顿检测"]
    E --> F["11 性能监控"]
```

1. **概要**（本文）— 建立性能优化全局认知
2. [`01-内存优化`](01-内存优化memory-optimization.md) — 泄漏检测、Bitmap 优化、OOM 防治
3. [`02-UI渲染性能`](02-UI渲染性能ui-rendering-performance.md) — 掉帧分析、布局优化、RecyclerView 调优
4. [`03-启动优化`](03-启动优化app-startup-optimization.md) — 耗时分析、异步初始化、Baseline Profile
5. [`04-卡顿检测与优化`](04-卡顿检测与优化jank-detection-and-optimization.md) — 主线程卡顿检测、BlockCanary、线上监控
6. [`11-性能监控与CI集成`](11-性能监控与CI集成performance-monitoring-and-ci.md) — 了解性能工程全流程

### 按需深入路径

已有基础的开发者，根据当前任务选择对应文件：

| 你的任务 | 推荐阅读 |
|----------|----------|
| 排查内存泄漏或 OOM | `01-内存优化` |
| 解决 UI 掉帧/滑动卡顿 | `02-UI渲染性能` |
| 优化应用启动速度 | `03-启动优化` |
| 检测和治理主线程卡顿 | `04-卡顿检测与优化` |
| 优化网络请求性能/弱网 | `05-网络性能优化` |
| 缩减 APK/AAB 体积 | `06-包体积优化` |
| 降低应用耗电量 | `07-功耗优化` |
| 优化数据库/文件/SP 性能 | `08-IO与存储性能` |
| 优化线程池/协程使用 | `09-多线程与协程性能` |
| 优化 Jetpack Compose 性能 | `10-Compose性能优化` |
| 搭建性能监控/CI 流水线 | `11-性能监控与CI集成` |

---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Android 基础开发
关联模块: 07-性能优化performance-optimization
---

# 视频播放 - 概要

## 模块定位

视频播放是 Android 客户端中最核心的多媒体能力之一。无论是短视频信息流、长视频点播、直播、视频通话，还是广告投放，都离不开一套成熟的视频播放技术体系。

本模块覆盖以下核心领域：

| 领域 | 说明 | 对应文件 |
|------|------|----------|
| 视频基础知识 | 编解码标准、容器格式、关键参数 | `01-视频基础知识` |
| 播放器选型 | 主流方案对比与决策 | `02-播放器选型` |
| Media3 实战 | Google 官方推荐方案的落地实践 | `03-Media3实战` |
| MediaCodec 编解码 | 底层硬件编解码 API | `04-MediaCodec编解码` |
| 流媒体协议 | HLS / DASH / RTMP / SRT 等 | `05-流媒体协议` |
| 播放器 UI 与交互 | 控制栏、手势、画中画、全屏 | `06-播放器UI与交互` |
| 缓存与离线播放 | 预加载、边下边播、离线下载 | `07-缓存与离线播放` |
| 字幕与多音轨 | 字幕渲染、音轨切换 | `08-字幕与多音轨` |
| DRM 与内容保护 | Widevine、数字版权管理 | `09-DRM与内容保护` |
| 性能优化与问题排查 | 首帧、卡顿、内存、功耗 | `10-性能优化与问题排查` |

## 知识全景图

```mermaid
mindmap
  root((视频播放模块))
    导航层
      00 概要
    基础层
      01 视频基础知识
        编解码标准
        容器格式
        关键参数
      02 播放器选型
        方案对比
        决策树
      03 Media3 实战
        ExoPlayer 核心用法
        自定义组件
    场景层
      04 MediaCodec 编解码
      05 流媒体协议
        HLS DASH
        RTMP SRT
      06 播放器 UI 与交互
      07 缓存与离线播放
      08 字幕与多音轨
    工程层
      09 DRM 与内容保护
      10 性能优化与问题排查
        首帧优化
        卡顿治理
        内存与功耗
```

## 模块间关系

视频播放并非孤立模块，它与项目中的多个横向能力存在依赖或协作关系：

```mermaid
graph TB
    video["视频播放模块"]

    subgraph external["关联模块"]
        perf["07-性能优化"]
        wifi["WiFi 稳定性"]
        i18n["国际化"]
        stability["系统稳定性"]
        network["网络层"]
    end

    video -->|首帧/卡顿/功耗指标| perf
    video -->|弱网自适应码率| wifi
    video -->|字幕本地化/RTL 布局| i18n
    video -->|Crash/ANR 治理| stability
    video -->|请求调度/CDN 切换| network

    style video fill:#e7f5ff,stroke:#1971c2,color:#000
    style perf fill:#d3f9d8,stroke:#2f9e44,color:#000
    style wifi fill:#fff4e6,stroke:#e67700,color:#000
    style i18n fill:#e5dbff,stroke:#5f3dc4,color:#000
    style stability fill:#ffe3e3,stroke:#c92a2a,color:#000
    style network fill:#c5f6fa,stroke:#0c8599,color:#000
```

## 推荐阅读路径

### 新人入门路径

适合刚接触 Android 视频播放的开发者，按顺序阅读：

```mermaid
graph LR
    A["00 概要"] --> B["01 视频基础知识"]
    B --> C["02 播放器选型"]
    C --> D["03 Media3 实战"]

    style A fill:#e7f5ff,stroke:#1971c2,color:#000
    style B fill:#d3f9d8,stroke:#2f9e44,color:#000
    style C fill:#d3f9d8,stroke:#2f9e44,color:#000
    style D fill:#d3f9d8,stroke:#2f9e44,color:#000
```

1. **概要**（本文）— 建立全局认知
2. **视频基础知识** — 理解编解码、容器、关键参数
3. **播放器选型** — 了解主流方案，做出技术选型
4. **Media3 实战** — 上手 Google 官方推荐方案

### 按需深入路径

已有基础的开发者，根据当前任务选择对应文件：

| 你的任务 | 推荐阅读 |
|----------|----------|
| 需要对接 HLS / DASH 流 | `05-流媒体协议` |
| 要做播放器 UI 或手势控制 | `06-播放器UI与交互` |
| 要实现边下边播 / 离线缓存 | `07-缓存与离线播放` |
| 要接入字幕或多音轨 | `08-字幕与多音轨` |
| 要集成 Widevine DRM | `09-DRM与内容保护` |
| 首帧慢、播放卡顿需要优化 | `10-性能优化与问题排查` |
| 需要直接操作 MediaCodec | `04-MediaCodec编解码` |

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

- [Android Media 官方文档](https://developer.android.com/media)
- [Media3 Release Notes](https://developer.android.com/jetpack/androidx/releases/media3)
- [ExoPlayer GitHub](https://github.com/google/ExoPlayer)
- [Android Supported Media Formats](https://developer.android.com/guide/topics/media/media-formats)

---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: 01-视频基础知识video-fundamentals
关联模块: 07-性能优化performance-optimization
---

# MediaCodec 编解码

## MediaCodec 在 Android 媒体架构中的位置

## API 核心概念

### 同步模式

### 异步模式（推荐）

### Buffer 管理机制

### Surface 输入 / 输出模式

## 视频解码

### 配置与启动解码器

### 逐帧解码流程

### Surface 输出 vs ByteBuffer 输出

### 解码器实例数量限制与复用

## 视频编码

### 录屏场景（MediaProjection + MediaCodec）

### Camera 录制场景（CameraX / Camera2 + MediaCodec）

### 编码参数调优（码率 / 帧率 / I 帧间隔 / Profile / Level）

### MediaMuxer 封装输出文件

## 视频转码

### Media3 Transformer API

### FFmpeg Android 方案

### 转码性能优化

## 帧提取与缩略图生成

### MediaMetadataRetriever 方案

### MediaCodec 逐帧解码方案

### 性能与精度对比

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

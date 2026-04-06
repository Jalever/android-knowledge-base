---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 待补充
前置知识: Android 资源管理基础、Gradle 构建基础
关联模块: 01-国际化internationalization/00-概要overview, 07-性能优化performance-optimization
---

# 资源本地化与按需分发

## 图片与 Drawable 本地化

### drawable 资源限定符

<!-- drawable-zh, drawable-ar, drawable-ldrtl 等 -->

### 需要本地化的图片类型

<!-- 含文字的图片、文化敏感图片、手势/方向相关图片 -->

### 矢量图 vs 位图的本地化策略

<!-- VectorDrawable autoMirrored vs 位图多套资源 -->

## 字体本地化

### 不同语言的字体需求

<!-- CJK 字体、阿拉伯文字体、特殊文字（泰文、缅甸文） -->

### Downloadable Fonts

<!-- 按需下载字体以减小包体积 -->

### 字体 Fallback 链

<!-- 系统字体 fallback 机制 -->

## 音视频与 TTS 多语言

### TTS 引擎与多语言支持

<!-- Android TTS 架构、引擎选择（Google TTS / 讯飞 / 百度） -->

### TTS 语言可用性检测

<!-- isLanguageAvailable() 检查与语音包下载引导 -->

### 音视频资源的多语言管理

<!-- 多语言配音、字幕文件的组织方式 -->

## App Bundle 按语言分包

### Language Split 原理

<!-- AAB 按语言自动拆分，用户仅下载所需语言 -->

### 配置 Language Split

<!-- bundle { language { enableSplit = true } } -->

### 额外语言安装

<!-- SplitInstallManager 动态请求额外语言 -->

## Play Asset Delivery

### 按需分发大型本地化资源

<!-- install-time / fast-follow / on-demand 模式 -->

### Asset Pack 的语言感知

<!-- 按语言组织 Asset Pack -->

## 多区域后端适配

### 多区域部署架构

<!-- 中国区、美区、欧洲区服务集群概述 -->

### Build Variant 方案

<!-- Product Flavor 按区域配置不同的 baseUrl -->

### 运行时动态切换

<!-- 根据用户区域动态切换后端地址 -->

### 数据合规考量

<!-- GDPR、中国数据出境规定等对架构的影响 -->

## 安装包体积优化

### 语言资源的体积分析

<!-- 各语言 strings.xml、字体、图片的体积占比 -->

### 按需加载 vs 全量打包的权衡

<!-- 首次体验 vs 包体积的平衡策略 -->

## 踩坑记录

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

<!-- Android 官方文档：App Bundle、Play Asset Delivery、TTS -->

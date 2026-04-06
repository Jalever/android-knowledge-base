---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: 性能优化概要（00-概要overview.md）、Android 构建流程基础、Gradle 基本用法
关联模块: 11-性能监控与CI集成performance-monitoring-and-ci
---

# 包体积优化

## APK 结构与分析

### APK 文件结构

> TODO: classes.dex、resources.arsc、res/、lib/、assets/、META-INF/ 各部分占比

### APK Analyzer 使用

> TODO: Android Studio 内置 APK Analyzer 的使用方法，各模块体积分析

### 包体积基线建立

> TODO: 记录初始包大小，按模块拆解体积占比，建立优化基线

## 代码优化

### R8 / ProGuard 代码缩减

> TODO: R8 Full Mode 配置、keep 规则编写、混淆映射文件管理

### 无用代码移除

> TODO: Dead Code Elimination、Lint 检查未使用代码、移除废弃功能模块

### 代码混淆与优化

> TODO: 类名/方法名缩短对 DEX 体积的影响、R8 优化级别

### Kotlin 特有优化

> TODO: @JvmStatic、inline 函数、data class copy 的体积影响

## 资源优化

### 无用资源移除

> TODO: Lint 检查 unused resources、shrinkResources 配置

### 图片资源压缩

> TODO: PNG 压缩（pngcrush/zopfli）、WebP 转换、AVIF 支持（API 31+）

### 资源混淆

> TODO: AndResGuard / AGP 内置资源混淆，缩短资源路径名

### 按需配置资源

> TODO: resConfigs 限制语言/密度、splits 按 ABI/密度分包

### 矢量图替代位图

> TODO: VectorDrawable / SVG 替代多套密度的 PNG，适用场景与限制

## SO 库优化

### ABI 过滤

> TODO: 只保留目标 ABI（arm64-v8a）、ndk.abiFilters 配置

### SO 库压缩与延迟加载

> TODO: 使用 uncompressedNativeLibs、动态下载 SO 库

### 裁剪不需要的 Native 符号

> TODO: strip 命令、-fvisibility=hidden 编译选项

## DEX 优化

### 方法数与 DEX 数量

> TODO: 64K 方法数限制、MultiDex 的体积开销

### D8 / R8 编译优化

> TODO: D8 vs DX 对比、R8 的 DEX 优化策略

### 移除调试信息

> TODO: 移除 line number / source file 信息对体积的影响

## 动态化方案

### Play Feature Delivery

> TODO: Dynamic Feature Module、按需下载功能模块

### 自建动态下发

> TODO: 插件化思路、资源/SO/DEX 按需下载

### AAB（Android App Bundle）

> TODO: AAB 格式原理、Google Play 自动分包、与 APK 体积对比

## 包体积监控与防劣化

### CI 包体积卡点

> TODO: 构建流水线中自动对比包大小，超阈值阻断合入

### 包体积归因分析

> TODO: 新增代码/资源/依赖对体积增量的自动归因

### 依赖库体积审计

> TODO: 定期审计第三方依赖对包体积的贡献

## 常见坑点

### 1. 引入大型依赖导致体积暴增

> TODO: 一个小功能引入整个 Guava/Apache Commons 的问题

### 2. 多语言资源未裁剪

> TODO: 第三方库携带几十种语言资源，未通过 resConfigs 过滤

### 3. shrinkResources 误删资源

> TODO: 动态引用资源被误判为无用资源的排查与 keep 方式

### 4. Debug 与 Release 包体积差异大

> TODO: Debug 包不经过 R8 优化，不能作为体积优化参考基准

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、开源项目、优质博客链接

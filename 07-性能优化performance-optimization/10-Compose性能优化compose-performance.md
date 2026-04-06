---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: 性能优化概要（00-概要overview.md）、Jetpack Compose 基础、Kotlin 基础
关联模块: 02-UI渲染性能ui-rendering-performance
---

# Compose 性能优化

## Compose 渲染原理

### 三阶段渲染模型

> TODO: Composition（组合）→ Layout（布局）→ Drawing（绘制）三阶段详解

### 重组（Recomposition）机制

> TODO: 状态变化如何触发重组、智能重组（只重新执行受影响的 Composable）

### Compose 与 View 系统渲染差异

> TODO: 声明式 vs 命令式、Compose 编译器的角色

## Recomposition 优化

### 避免不必要的重组

> TODO: Composable 函数的幂等性原则、状态读取位置对重组范围的影响

### Stability 系统

> TODO: Stable / Unstable / Immutable 注解、Compose Compiler 如何判定参数稳定性

### Compose Compiler Metrics

> TODO: 生成 Stability 报告的 Gradle 配置、如何解读报告、修复 Unstable 参数

### Strong Skipping Mode

> TODO: Compose Compiler 1.5.4+ 的 Strong Skipping Mode 原理与启用方式

## 状态管理性能

### remember 与 rememberSaveable

> TODO: remember 的缓存机制、何时使用 rememberSaveable、避免在 remember 中执行耗时计算

### derivedStateOf

> TODO: 减少因依赖状态频繁变化导致的不必要重组，适用场景与示例

### snapshotFlow

> TODO: 将 Compose State 转换为 Flow，在需要时才响应变化

### State 持有者模式

> TODO: 将复杂状态逻辑提取到专门的 State Holder 类，减少 Composable 重组范围

## LazyColumn / LazyGrid 性能

### key 的正确使用

> TODO: 为 LazyColumn items 指定稳定 key，避免不必要的重新创建和重组

### contentType 优化

> TODO: 为不同类型的列表项指定 contentType，优化 ViewHolder 复用

### 避免在 LazyColumn 中嵌套可滚动组件

> TODO: 嵌套滚动的性能问题与替代方案

### 列表项 Composable 优化

> TODO: 保持列表项 Composable 轻量、避免在列表项中进行复杂计算

## 布局性能

### Modifier 顺序的影响

> TODO: Modifier 链的执行顺序对测量和绘制的影响

### SubcomposeLayout 的性能特征

> TODO: SubcomposeLayout 的延迟组合特性、适用与不适用场景

### 避免不必要的 Layout 阶段重复

> TODO: Intrinsic Measurements 的开销、Layout 阶段的缓存机制

### BoxWithConstraints 的性能代价

> TODO: BoxWithConstraints 使用 SubcomposeLayout 实现，比普通 Box 开销更大

## 绘制性能

### Canvas 绘制优化

> TODO: Compose Canvas 与 View Canvas 的性能差异、drawWithCache 的使用

### 图片加载性能

> TODO: Coil-Compose / Glide-Compose 的最佳实践、placeholder 与 crossfade

### 动画性能

> TODO: animate*AsState vs Animatable、updateTransition 的性能特征、避免动画触发重组

## Compose 与 View 互操作性能

### AndroidView 的使用代价

> TODO: AndroidView 在 Composition 中的开销、何时应该使用 AndroidView

### ComposeView 嵌入传统布局

> TODO: ComposeView 初始化开销、在 RecyclerView ViewHolder 中使用 Compose 的注意事项

## Compose 性能分析工具

### Layout Inspector（Compose 模式）

> TODO: 查看 Composable 树、重组次数计数、跳过次数

### Compose Compiler Metrics / Reports

> TODO: composable 函数的稳定性、可跳过性报告生成与解读

### Perfetto Trace 中的 Compose

> TODO: Composition / Layout / Drawing 在 Perfetto 中的 Trace 标记

### Recomposition Highlighter

> TODO: 开发工具中高亮显示正在重组的 Composable

## 常见坑点

### 1. 在 Composable 中创建新对象导致重组

> TODO: 每次重组创建新 List/Map/Lambda 导致子 Composable 判定参数变化

### 2. 未使用 key 导致 LazyColumn 性能退化

> TODO: 缺少稳定 key 时列表滑动触发大量不必要的重组和重新创建

### 3. collectAsState 在高频 Flow 上的性能问题

> TODO: 高频状态更新导致过于频繁的重组，应使用 conflate 或 debounce

### 4. 过度使用 derivedStateOf

> TODO: derivedStateOf 本身有创建快照读取的开销，不适合简单的状态转换

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、开源项目、优质博客链接

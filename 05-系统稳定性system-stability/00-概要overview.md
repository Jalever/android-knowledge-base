---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: Android 四大组件基础、Handler 消息机制、Linux 进程管理基础
关联模块: 07-性能优化performance-optimization, 06-WiFi稳定性wifi-stability
---

# 系统稳定性 - 概要

## 模块定位

> TODO: 描述系统稳定性模块在团队知识库中的定位，覆盖从 Crash/ANR 分类、内存治理、Native 崩溃分析，到异常恢复、线上治理、稳定性测试的完整知识体系。

| 领域 | 说明 | 对应文件 |
|------|------|----------|
| Crash 基础与分类 | Java Crash、Native Crash、System Server Crash、Kernel Panic | `01-Crash基础与分类` |
| ANR 深度解析 | ANR 触发机制、超时阈值、traces.txt 解读、预防实践 | `02-ANR深度解析` |
| 内存稳定性与 OOM | 内存模型、Low Memory Killer、OOM 类型、泄漏检测 | `03-内存稳定性与OOM` |
| Native Crash 与 Tombstone | 信号机制、Tombstone 解读、符号化还原、GWP-ASan | `04-NativeCrash与Tombstone分析` |
| Crash 采集与上报 | UncaughtExceptionHandler、xCrash、Crashlytics、符号化流水线 | `05-Crash采集与上报` |
| Watchdog 与系统级监控 | Framework/Kernel/硬件 Watchdog、DropBoxManager | `06-Watchdog与系统级监控` |
| 异常恢复与容错设计 | 崩溃恢复策略、状态恢复、优雅降级、进程保活 | `07-异常恢复与容错设计` |
| 稳定性调试工具链 | StrictMode、Profiler、Perfetto、bugreport、Logcat | `08-稳定性调试工具链` |
| 线上稳定性治理 | 度量指标、监控架构、告警策略、质量门禁 | `09-线上稳定性治理` |
| 稳定性测试与防劣化 | Monkey 测试、自动化测试、CI 集成、基线回归检测 | `10-稳定性测试与防劣化` |

## 知识全景图

> TODO: Mermaid mindmap，按导航层 / 基础层 / 核心层 / 场景层 / 工程层组织 00-10 的子主题。

## 核心原理

> TODO: 多层防御体系概览（应用层 / Framework 层 / Native 层 / 内核层），四大核心机制速览（Watchdog / ANR 检测 / Crash Handler / Low Memory Killer）。

## Crash 四大分类速览

> TODO: Java Crash / Native Crash / System Server Crash / Kernel Panic 简明对比表。

## ANR 核心原理速览

> TODO: ANR 本质、超时阈值表、与 Crash 的区别。

## 发展趋势

> TODO: Android 8.0 - 15 各版本稳定性相关变化表。

## 主流方案与开源项目对比

> TODO: Crashlytics / Bugly / Sentry / xCrash / ACRA 对比表 + 选型建议流程图。

## 模块间关系

> TODO: Mermaid 关系图，关联 07-性能优化、06-WiFi 稳定性等模块。

## 推荐阅读路径

### 新人入门路径

> TODO: 00 概要 → 01 Crash 基础 → 02 ANR 解析 → 05 Crash 采集 → 08 调试工具链

### 按需深入路径

> TODO: 任务 → 推荐文件 对照表。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、开源项目、优质博客链接。

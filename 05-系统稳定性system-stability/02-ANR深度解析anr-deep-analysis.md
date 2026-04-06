---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 草稿
前置知识: Handler 消息机制、Android 四大组件生命周期、主线程模型
关联模块: 05-系统稳定性system-stability/00-概要overview, 05-系统稳定性system-stability/08-稳定性调试工具链stability-debugging-toolchain
---

# ANR 深度解析

## ANR 触发机制原理

### AMS / InputDispatcher 计时器模型

> TODO: ANR 的本质（主线程在规定时间内未完成预期工作）、AMS 和 InputDispatcher 各自的计时逻辑、时序图。

### 四类 ANR 超时阈值详解

> TODO: InputDispatching（5s）、BroadcastReceiver（前台 10s / 后台 60s）、Service（前台 20s / 后台 200s）、ContentProvider（10s，Android 12+）。

## traces.txt 完整解读

### 文件位置与获取方式

> TODO: /data/anr/traces.txt 路径、adb pull、bugreport、ApplicationExitInfo API 三种获取方式。

### 关键字段解读

> TODO: 线程状态（Blocked / Waiting / Sleeping / Running）、waiting to lock、held mutexes、utm / stm 含义。

### 死锁环检测方法

> TODO: 如何从 traces.txt 中识别 A 等 B、B 等 A 的死锁环。

## ANR 常见原因分类

### 主线程 IO

> TODO: 数据库查询、文件读写、SharedPreferences commit。

### 锁竞争

> TODO: synchronized 死锁、ReentrantLock 超时、ContentProvider 跨进程锁。

### Binder 调用阻塞

> TODO: 系统服务繁忙、跨进程调用超时、ServiceConnection 阻塞。

### CPU 资源不足

> TODO: 后台进程占用、系统 GC 频繁、低端设备性能瓶颈。

### 消息队列积压

> TODO: 大量 Handler.post、主线程 Choreographer 掉帧。

## adb bugreport 中 ANR 分析

> TODO: bugreport 中 ANR 相关关键段落（"ANR in"、"CPU usage"、"iowait"）解读方法。

## ANR 预防最佳实践

### 协程化异步处理

> TODO: 将耗时操作迁移到协程 / 子线程的模式与示例。

### 避免主线程 IO

> TODO: StrictMode 检测 + 常见违规场景修复。

### 锁优化策略

> TODO: 减小锁粒度、使用读写锁、避免嵌套锁。

### Binder 调用优化

> TODO: 异步 Binder、超时控制、避免在主线程调用系统服务。

## 线上 ANR 检测方案

### 自定义主线程 Watchdog

> TODO: 基于 HandlerThread 的 ANR 检测实现思路。

### FileObserver 监听方案

> TODO: 监听 /data/anr/ 目录变化捕获 ANR。

## 常见坑点

> TODO: 补充实际开发中的典型 ANR 问题。

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

> TODO: 补充官方文档、源码链接、相关博客。

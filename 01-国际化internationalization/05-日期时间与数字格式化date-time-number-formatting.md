---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 待补充
前置知识: Java 日期时间 API 基础、Kotlin 基础
关联模块: 01-国际化internationalization/01-Locale与资源加载机制locale-and-resource-loading
---

# 日期时间与数字格式化

## 日期与时间格式化

### DateFormat 与 SimpleDateFormat

<!-- Locale-aware 的 DateFormat.getDateInstance() 用法 -->

### java.time API（API 26+）

<!-- LocalDate, LocalDateTime, DateTimeFormatter 的 locale 感知 -->

### android.icu.text.DateFormat

<!-- ICU4J 提供的更强大的日期格式化（API 24+） -->

### 各地区日期格式差异

<!-- 表格：中国 2026/04/06、美国 04/06/2026、德国 06.04.2026 等 -->

## 时区处理

### TimeZone 与 ZoneId

<!-- 时区表示与转换基础 -->

### 跨时区 UI 展示策略

<!-- 本地时间 vs UTC 时间 vs 用户所选时区 -->

### 服务端时间与客户端时间同步

<!-- 统一使用 UTC 存储、按用户时区显示 -->

## 数字格式化

### NumberFormat 的 Locale-aware 用法

<!-- NumberFormat.getInstance(locale) -->

### 千位分隔符与小数点差异

<!-- 表格：1,000.00 vs 1.000,00 vs 1 000,00 -->

### 百分比格式

<!-- NumberFormat.getPercentInstance() -->

## 货币格式化

### Currency 类与货币代码

<!-- ISO 4217 货币代码 -->

### NumberFormat.getCurrencyInstance()

<!-- 货币符号位置与格式差异：$100 vs 100€ vs ¥100 -->

### 多币种展示策略

<!-- 应用中同时展示多种货币的 UI 设计 -->

## 度量单位

### MeasureFormat（ICU4J）

<!-- 长度、重量、温度等单位的本地化表示 -->

### 公制 vs 英制

<!-- 根据地区自动选择度量体系 -->

## 日历系统

### 公历与其他日历

<!-- 伊斯兰历、日本年号、佛历等 -->

### android.icu.util.Calendar

<!-- ICU 日历支持 -->

## 排序与比较

### Collator

<!-- Locale-aware 字符串排序 -->

### 各语言排序规则差异

<!-- 中文拼音排序、德语 ä/ö/ü 排序等 -->

## 踩坑记录

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

<!-- Android 官方文档：ICU4J、DateFormat、NumberFormat -->

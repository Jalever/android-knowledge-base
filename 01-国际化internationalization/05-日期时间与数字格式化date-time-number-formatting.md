---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Java 日期时间 API 基础、Kotlin 基础
关联模块: 01-国际化internationalization/01-Locale与资源加载机制locale-and-resource-loading
---

# 日期时间与数字格式化

## 日期与时间格式化

### DateFormat 与 SimpleDateFormat

`java.text.DateFormat` 提供了 Locale-aware 的日期格式化能力：

```kotlin
val date = Date()
val locale = context.resources.configuration.locales[0]

// 使用系统预设格式（推荐）
val shortDate = DateFormat.getDateInstance(DateFormat.SHORT, locale).format(date)
val mediumDate = DateFormat.getDateInstance(DateFormat.MEDIUM, locale).format(date)
val longDate = DateFormat.getDateInstance(DateFormat.LONG, locale).format(date)
val dateTime = DateFormat.getDateTimeInstance(
    DateFormat.MEDIUM, DateFormat.SHORT, locale
).format(date)
```

| 风格 | 中文（zh-CN） | 英文（en-US） | 德文（de-DE） |
|------|-------------|-------------|-------------|
| SHORT | 2026/4/6 | 4/6/26 | 06.04.26 |
| MEDIUM | 2026年4月6日 | Apr 6, 2026 | 06.04.2026 |
| LONG | 2026年4月6日 | April 6, 2026 | 6. April 2026 |

> **避免硬编码格式**：不要用 `SimpleDateFormat("yyyy-MM-dd")` 展示给用户看的日期，因为 `yyyy-MM-dd` 不符合多数地区的日期习惯。仅在与服务端通信时使用固定格式。

### java.time API（API 26+）

```kotlin
val now = LocalDateTime.now()
val locale = context.resources.configuration.locales[0]

// 预定义格式（Locale-aware）
val formatter = DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)
    .withLocale(locale)
val formatted = now.format(formatter)

// 自定义模式（面向用户时仍需考虑地区差异）
val custom = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm", locale)
val customFormatted = now.format(custom)
```

> API < 26 可通过 `coreLibraryDesugaring` 使用 java.time：
> ```kotlin
> // build.gradle.kts
> android { compileOptions { isCoreLibraryDesugaringEnabled = true } }
> dependencies { coreLibraryDesugaring("com.android.tools:desugar_jdk_libs:2.1.4") }
> ```

### android.icu.text.DateFormat

Android 7.0（API 24）起内置 ICU4J，提供更强大的日期格式化，特别是对日历系统的支持：

```kotlin
import android.icu.text.DateFormat as IcuDateFormat
import android.icu.util.ULocale

val icuFormat = IcuDateFormat.getDateInstance(
    IcuDateFormat.MEDIUM,
    ULocale("ar-SA@calendar=islamic")
)
val arabicDate = icuFormat.format(Date())
// 输出示例: ١٢ شوال ١٤٤٧
```

### 各地区日期格式差异

| 地区 | 短日期格式 | 完整示例 |
|------|-----------|---------|
| 中国（zh-CN） | yyyy/M/d | 2026/4/6 |
| 美国（en-US） | M/d/yy | 4/6/26 |
| 英国（en-GB） | dd/MM/yyyy | 06/04/2026 |
| 德国（de-DE） | dd.MM.yy | 06.04.26 |
| 日本（ja-JP） | yyyy/MM/dd | 2026/04/06 |
| 韩国（ko-KR） | yy. M. d. | 26. 4. 6. |
| 阿拉伯（ar-SA） | d/M/yyyy | ٦/٤/٢٠٢٦ |

## 时区处理

### TimeZone 与 ZoneId

```kotlin
// java.util.TimeZone（传统 API）
val timeZone = TimeZone.getDefault()  // 设备时区
val utcZone = TimeZone.getTimeZone("UTC")

// java.time.ZoneId（推荐，API 26+）
val zoneId = ZoneId.systemDefault()
val tokyoZone = ZoneId.of("Asia/Tokyo")
val utcZoneId = ZoneId.of("UTC")
```

### 跨时区 UI 展示策略

| 场景 | 推荐方案 | 说明 |
|------|----------|------|
| 消息发送时间 | 本地时区 | 用户关心的是"多久前"而非绝对时间 |
| 航班/酒店预订 | 目的地时区 | 明确标注时区，如 "14:30 (GMT+9)" |
| 跨国会议 | 多时区 | 同时展示参会者所在时区的时间 |
| 日志/审计 | UTC | 无歧义，方便排查 |

```kotlin
// 展示跨时区时间
fun formatWithTimezone(
    instant: Instant,
    zoneId: ZoneId,
    locale: Locale
): String {
    val zonedDateTime = instant.atZone(zoneId)
    val formatter = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM)
        .withLocale(locale)
    val timeZoneAbbr = zonedDateTime.zone.getDisplayName(TextStyle.SHORT, locale)
    return "${zonedDateTime.format(formatter)} ($timeZoneAbbr)"
}
```

### 服务端时间与客户端时间同步

**核心原则**：服务端统一使用 UTC 存储和传输，客户端按用户时区显示。

```kotlin
// 服务端返回 UTC 时间戳（ISO 8601）
val serverTime = "2026-04-06T08:30:00Z"

// 客户端解析并转换为本地时间
val instant = Instant.parse(serverTime)
val localDateTime = instant.atZone(ZoneId.systemDefault())
    .toLocalDateTime()
```

## 数字格式化

### NumberFormat 的 Locale-aware 用法

```kotlin
val locale = context.resources.configuration.locales[0]
val number = 1234567.89

// 通用数字格式
val formatted = NumberFormat.getInstance(locale).format(number)

// 整数格式（无小数部分）
val intFormatted = NumberFormat.getIntegerInstance(locale).format(number)
```

### 千位分隔符与小数点差异

不同地区对数字的分隔方式差异很大：

| 地区 | 格式示例 | 千位分隔符 | 小数点 |
|------|----------|:----------:|:------:|
| 美国/中国/日本 | 1,234,567.89 | `,` | `.` |
| 德国/法国/俄罗斯 | 1.234.567,89 | `.` | `,` |
| 瑞士（法语区） | 1 234 567,89 | 空格 | `,` |
| 印度 | 12,34,567.89 | `,`（不规则） | `.` |

```kotlin
// ❌ 错误：硬编码数字格式
val text = String.format("%,.2f", 1234567.89) // 使用 JVM 默认 Locale

// ✅ 正确：使用 Locale-aware 格式化
val formatter = NumberFormat.getInstance(locale).apply {
    minimumFractionDigits = 2
    maximumFractionDigits = 2
}
val text = formatter.format(1234567.89)
```

### 百分比格式

```kotlin
val locale = context.resources.configuration.locales[0]
val percentage = 0.856

val formatter = NumberFormat.getPercentInstance(locale)
val text = formatter.format(percentage)
// en-US: "86%"
// de-DE: "86 %"（注意百分号前有空格）
// tr-TR: "%86"（百分号在前面）
```

## 货币格式化

### Currency 类与货币代码

```kotlin
// ISO 4217 货币代码
val usd = Currency.getInstance("USD")
val eur = Currency.getInstance("EUR")
val cny = Currency.getInstance("CNY")
val jpy = Currency.getInstance("JPY")

// 获取货币信息
usd.symbol          // "$"
usd.displayName     // "US Dollar"（取决于 Locale）
jpy.defaultFractionDigits  // 0（日元无小数位）
usd.defaultFractionDigits  // 2
```

### NumberFormat.getCurrencyInstance()

```kotlin
val amount = 1234.56
val locale = Locale("en", "US")
val formatter = NumberFormat.getCurrencyInstance(locale)
val text = formatter.format(amount) // "$1,234.56"

// 不同地区的货币格式
// en-US: $1,234.56
// de-DE: 1.234,56 €
// ja-JP: ￥1,235（日元无小数）
// zh-CN: ¥1,234.56
// ar-SA: ١٬٢٣٤٫٥٦ ر.س.
```

### 多币种展示策略

当应用需要同时展示多种货币时：

```kotlin
fun formatCurrency(amount: Double, currencyCode: String, locale: Locale): String {
    val formatter = NumberFormat.getCurrencyInstance(locale)
    formatter.currency = Currency.getInstance(currencyCode)
    return formatter.format(amount)
}

// 使用 ISO 货币代码消除歧义
// "$100" 可能是美元、加元、澳元...
// 使用 "USD 100.00" 或 "US$100.00" 更清晰
fun formatCurrencyWithCode(amount: Double, currencyCode: String): String {
    val currency = Currency.getInstance(currencyCode)
    val formatter = NumberFormat.getCurrencyInstance(Locale.US)
    formatter.currency = currency
    return "$currencyCode ${formatter.format(amount)}"
}
```

## 度量单位

### MeasureFormat（ICU4J）

Android 的 ICU4J（API 24+）提供了 `MeasureFormat` 用于本地化度量单位：

```kotlin
import android.icu.text.MeasureFormat
import android.icu.text.MeasureFormat.FormatWidth
import android.icu.util.Measure
import android.icu.util.MeasureUnit

val locale = ULocale("zh-CN")
val formatter = MeasureFormat.getInstance(locale, FormatWidth.SHORT)

val distance = Measure(42.195, MeasureUnit.KILOMETER)
formatter.format(distance) // "42.195公里"

val temp = Measure(36.5, MeasureUnit.CELSIUS)
formatter.format(temp) // "36.5°C"
```

### 公制 vs 英制

| 地区 | 温度 | 距离 | 重量 |
|------|------|------|------|
| 中国/大多数国家 | 摄氏度 °C | 公里 km | 千克 kg |
| 美国 | 华氏度 °F | 英里 mi | 磅 lb |
| 英国 | 摄氏度 °C | 英里 mi（道路）/ 公里 | 磅 / 千克（混用） |

```kotlin
fun getTemperatureUnit(locale: Locale): String {
    return when (locale.country) {
        "US" -> "°F"
        else -> "°C"
    }
}
```

## 日历系统

### 公历与其他日历

世界上有多种日历系统在使用：

| 日历 | 使用地区 | 当前年份（约） |
|------|----------|:-----------:|
| 公历（格里高利历） | 全球通用 | 2026 |
| 伊斯兰历（回历） | 中东、东南亚穆斯林 | 1447 |
| 日本年号 | 日本（与公历并用） | 令和 8 |
| 佛历 | 泰国、缅甸、柬埔寨 | 2569 |
| 希伯来历 | 以色列 | 5786 |

### android.icu.util.Calendar

```kotlin
import android.icu.util.Calendar
import android.icu.util.IslamicCalendar
import android.icu.util.JapaneseCalendar
import android.icu.util.ULocale

// 伊斯兰历
val islamicCal = Calendar.getInstance(ULocale("ar-SA@calendar=islamic"))
val islamicYear = islamicCal.get(Calendar.YEAR)   // 1447
val islamicMonth = islamicCal.get(Calendar.MONTH)  // 月份

// 日本年号
val japaneseCal = Calendar.getInstance(ULocale("ja-JP@calendar=japanese"))
val era = japaneseCal.get(Calendar.ERA)  // 令和时代
val year = japaneseCal.get(Calendar.YEAR) // 年号年
```

## 排序与比较

### Collator

`Collator` 提供 Locale-aware 的字符串排序，考虑各语言特有的排序规则：

```kotlin
val locale = context.resources.configuration.locales[0]
val collator = Collator.getInstance(locale)

val names = listOf("张三", "李四", "王五", "赵六")
val sorted = names.sortedWith(collator) // 按拼音排序

// 用于 RecyclerView 数据排序
adapter.submitList(items.sortedWith(compareBy(collator) { it.name }))
```

### 各语言排序规则差异

| 语言 | 排序规则 |
|------|----------|
| 中文 | 按拼音排序（A-Z），不是按笔画 |
| 德文 | ä 排在 a 之后（而非 ae） |
| 瑞典语 | å、ä、ö 排在 z 之后 |
| 日文 | あいうえお 五十音顺序 |
| 阿拉伯语 | 特有的字母顺序 |

```kotlin
// 德语排序示例
val collator = Collator.getInstance(Locale.GERMAN)
val words = listOf("Apfel", "Ärger", "Birne", "Über")
val sorted = words.sortedWith(collator)
// 结果: [Apfel, Ärger, Birne, Über]（ä 紧跟在 a 之后）

// 不用 Collator 的结果
val wrongSort = words.sorted()
// 结果: [Apfel, Birne, Über, Ärger]（ä 被排到最后，因为 Unicode 码点大）
```

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

- [Android 官方文档 - ICU4J 框架](https://developer.android.com/guide/topics/resources/icu4j-framework)
- [ICU 官方文档 - 格式化](https://unicode-org.github.io/icu/userguide/format_parse/)
- [Java DateFormat API](https://developer.android.com/reference/java/text/DateFormat)
- [Java NumberFormat API](https://developer.android.com/reference/java/text/NumberFormat)
- [ISO 4217 货币代码](https://www.iso.org/iso-4217-currency-codes.html)
- [Unicode CLDR - 日历数据](https://cldr.unicode.org/development/development-process/design-proposals/calendar-data)
- [Android coreLibraryDesugaring](https://developer.android.com/studio/write/java8-support-table)

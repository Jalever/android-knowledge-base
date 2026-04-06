---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Android 资源管理基础、Kotlin 基础
关联模块: 01-国际化internationalization/00-概要overview, 01-国际化internationalization/01-Locale与资源加载机制locale-and-resource-loading
---

# 字符串与翻译管理

## strings.xml 资源管理

### 命名规范

采用 **模块_组件_用途** 的分层命名方式，保持全局唯一且易于检索：

```xml
<!-- ✅ 推荐命名 -->
<string name="login_btn_submit">登录</string>
<string name="login_input_hint_username">请输入用户名</string>
<string name="login_error_invalid_password">密码格式不正确</string>
<string name="settings_title">设置</string>
<string name="common_btn_cancel">取消</string>
<string name="common_btn_confirm">确认</string>

<!-- ❌ 不推荐命名 -->
<string name="text1">登录</string>
<string name="btn">取消</string>
<string name="title">设置</string>
<string name="msg">操作成功</string>
```

| 层级 | 前缀示例 | 说明 |
|------|----------|------|
| 模块 | `login_`、`settings_`、`payment_` | 功能模块名 |
| 组件 | `btn_`、`input_`、`title_`、`msg_` | UI 组件类型 |
| 用途 | `submit`、`hint_username`、`error_network` | 具体用途 |
| 通用 | `common_` | 跨模块共享的字符串 |

### 分模块组织

对于大型项目，将字符串按功能模块拆分到不同文件，避免单个 strings.xml 膨胀到数千行：

```
res/values/
├── strings.xml              # 通用字符串（common_前缀）
├── strings_login.xml        # 登录模块
├── strings_settings.xml     # 设置模块
├── strings_payment.xml      # 支付模块
└── strings_error.xml        # 全局错误信息
```

> Android 资源系统会自动合并同目录下所有 XML 文件，因此拆分后不影响代码中的引用方式。

### 避免硬编码

```kotlin
// ❌ 硬编码字符串
textView.text = "加载中..."
Toast.makeText(context, "操作成功", Toast.LENGTH_SHORT).show()

// ✅ 使用资源引用
textView.text = getString(R.string.common_loading)
Toast.makeText(context, R.string.common_success, Toast.LENGTH_SHORT).show()
```

**Lint 配置**：开启 `HardcodedText` 检查，在 CI 中强制不允许硬编码字符串：

```xml
<!-- lint.xml -->
<lint>
    <issue id="HardcodedText" severity="error" />
    <issue id="SetTextI18n" severity="error" />
</lint>
```

## 复数处理（Plurals）

### 各语言复数规则差异

不同语言的复数规则差异巨大，不能简单地用 `if (count == 1)` 判断：

| 语言 | 复数形式数量 | 规则说明 |
|------|:----------:|----------|
| 中文/日文/韩文 | 1 | 无复数变化，统一使用 `other` |
| 英语 | 2 | `one`（1）、`other`（其余） |
| 法语 | 2 | `one`（0 和 1）、`other`（其余，注意 0 也是 one） |
| 俄语 | 3 | `one`、`few`、`other` |
| 阿拉伯语 | 6 | `zero`、`one`、`two`、`few`、`many`、`other` |
| 波兰语 | 3 | `one`、`few`、`other`（规则复杂） |

### plurals 资源定义与使用

```xml
<!-- res/values/strings.xml（默认/英文） -->
<plurals name="item_count">
    <item quantity="one">%d item</item>
    <item quantity="other">%d items</item>
</plurals>

<!-- res/values-zh-rCN/strings.xml -->
<plurals name="item_count">
    <item quantity="other">%d 个项目</item>
</plurals>

<!-- res/values-ar/strings.xml -->
<plurals name="item_count">
    <item quantity="zero">لا عناصر</item>
    <item quantity="one">عنصر واحد</item>
    <item quantity="two">عنصران</item>
    <item quantity="few">%d عناصر</item>
    <item quantity="many">%d عنصرًا</item>
    <item quantity="other">%d عنصر</item>
</plurals>
```

```kotlin
val count = 5
val text = resources.getQuantityString(R.plurals.item_count, count, count)
// 英文输出: "5 items"
// 中文输出: "5 个项目"
```

> **注意**：`getQuantityString` 的第二个参数 `count` 用于选择复数形式，第三个参数用于格式化填充。两者通常相同但必须都传。

### 复数处理的常见错误

```kotlin
// ❌ 错误：手动判断复数
val text = if (count == 1) "$count item" else "$count items"

// ❌ 错误：遗漏 quantity 类型（阿拉伯语需要 6 种形式）
// 仅提供 one 和 other 会导致阿拉伯语复数显示异常

// ✅ 正确：使用 plurals 资源，由系统根据语言规则选择
val text = resources.getQuantityString(R.plurals.item_count, count, count)
```

## 格式化字符串

### 位置参数

**始终使用位置参数（`%1$s`、`%2$d`）** 而非简单的 `%s`、`%d`，因为不同语言的语序可能不同：

```xml
<!-- 英文语序：Tom uploaded 5 photos -->
<string name="upload_message">%1$s uploaded %2$d photos</string>

<!-- 日文语序：Tomは写真を5枚アップロードしました -->
<string name="upload_message">%1$sは写真を%2$d枚アップロードしました</string>

<!-- 如果用 %s 和 %d 而非位置参数，日文翻译无法调整语序 -->
```

```kotlin
val message = getString(R.string.upload_message, userName, photoCount)
```

### HTML 标记与 Span

strings.xml 支持基础 HTML 标签用于富文本：

```xml
<!-- 支持的基础标签 -->
<string name="terms"><b>注意：</b>继续即表示同意<i>服务条款</i></string>

<!-- 含 HTML 实体或复杂标签时使用 CDATA -->
<string name="promo"><![CDATA[立省 <b>50%</b>，仅限 <font color="#FF0000">今天</font>]]></string>
```

```kotlin
// 基础标签可直接使用
textView.text = getText(R.string.terms)  // 返回 SpannedString

// CDATA 内容需要 Html.fromHtml 解析
textView.text = HtmlCompat.fromHtml(
    getString(R.string.promo),
    HtmlCompat.FROM_HTML_MODE_COMPACT
)
```

### 字符串拼接的反模式

```kotlin
// ❌ 反模式：字符串拼接
val msg = getString(R.string.hello) + userName + getString(R.string.welcome_suffix)
// 问题：不同语言中 "hello" 和 "suffix" 的位置可能完全不同

// ❌ 反模式：用 String.format 但不用位置参数
val msg = String.format("%s, welcome back! You have %d messages.", name, count)

// ✅ 正确做法：使用格式化字符串资源
// <string name="welcome_back">%1$s，欢迎回来！你有 %2$d 条消息。</string>
val msg = getString(R.string.welcome_back, name, count)
```

## String Arrays 与 Typed Arrays

### 字符串数组的国际化

```xml
<!-- res/values/strings.xml -->
<string-array name="weekdays">
    <item>Monday</item>
    <item>Tuesday</item>
    <item>Wednesday</item>
    <item>Thursday</item>
    <item>Friday</item>
    <item>Saturday</item>
    <item>Sunday</item>
</string-array>

<!-- res/values-zh-rCN/strings.xml -->
<string-array name="weekdays">
    <item>星期一</item>
    <item>星期二</item>
    <item>星期三</item>
    <item>星期四</item>
    <item>星期五</item>
    <item>星期六</item>
    <item>星期日</item>
</string-array>
```

```kotlin
val weekdays = resources.getStringArray(R.array.weekdays)
```

> **注意**：各语言的 string-array 中 item 数量必须一致，否则可能导致数组越界。

### TypedArray 的国际化场景

当本地化数据包含非字符串类型（如颜色、尺寸）时，可将 TypedArray 按语言提供不同版本：

```xml
<!-- values/arrays.xml - 默认字体大小 -->
<array name="text_sizes">
    <item>14sp</item>  <!-- 英文 -->
    <item>16sp</item>
</array>

<!-- values-zh/arrays.xml - 中文字体可能需要更大 -->
<array name="text_sizes">
    <item>15sp</item>
    <item>17sp</item>
</array>
```

## 翻译文件管理策略

### 默认语言兜底原则

**核心原则**：默认资源文件 `values/strings.xml` 必须包含应用中使用的所有字符串 key，作为最终兜底。

```
values/strings.xml          → 500 个 key（✅ 包含所有 key）
values-zh-rCN/strings.xml   → 500 个 key（✅ 完整翻译）
values-ja/strings.xml       → 480 个 key（⚠️ 缺少 20 个，将 fallback 到默认）
values-ar/strings.xml       → 300 个 key（⚠️ 缺少 200 个，将 fallback 到默认）
```

### Key 一致性校验

所有翻译文件的 key 必须是默认文件的**子集**（不允许出现默认文件中不存在的 key）：

```bash
#!/bin/bash
# check_translation_keys.sh
DEFAULT_FILE="app/src/main/res/values/strings.xml"
DEFAULT_KEYS=$(grep -oP 'name="\K[^"]+' "$DEFAULT_FILE" | sort)

for LANG_FILE in app/src/main/res/values-*/strings.xml; do
    LANG_KEYS=$(grep -oP 'name="\K[^"]+' "$LANG_FILE" | sort)
    EXTRA_KEYS=$(comm -23 <(echo "$LANG_KEYS") <(echo "$DEFAULT_KEYS"))
    if [ -n "$EXTRA_KEYS" ]; then
        echo "⚠️ $LANG_FILE 包含默认文件中不存在的 key："
        echo "$EXTRA_KEYS"
    fi
    MISSING=$(comm -23 <(echo "$DEFAULT_KEYS") <(echo "$LANG_KEYS") | wc -l)
    echo "📊 $LANG_FILE: 缺少 $MISSING 个翻译"
done
```

### 未使用字符串清理

随着版本迭代，部分字符串 key 可能已无引用。定期清理可以减少翻译负担和包体积：

```bash
# 使用 Android Lint 检测未使用的资源
./gradlew lint --check UnusedResources

# 或使用 Android Studio: Analyze > Run Inspection by Name > Unused Resources
```

> **注意**：Lint 的 `UnusedResources` 检查无法覆盖通过反射或动态方式引用的字符串。对于动态引用的资源，需在 `lint.xml` 中排除或在 `tools:keep` 中标记。

## 动态文本与资源的边界

### 服务端下发文本 vs 本地资源

| 场景 | 推荐方案 | 说明 |
|------|----------|------|
| 固定 UI 文案（按钮、标签、提示） | 本地 strings.xml | 随版本发布，不频繁变更 |
| 运营活动文案 | 服务端下发 | 频繁变更，不受发版周期限制 |
| 用户生成内容（评论、消息） | 服务端下发 | 内容动态变化 |
| 法律条款、隐私协议 | 服务端下发 | 可能随法规变化紧急更新 |
| 错误提示信息 | 本地 strings.xml | 离线时也需要显示 |

### 动态文本的 fallback 策略

当服务端文本缺少某语言的翻译时，客户端需要合理 fallback：

```kotlin
data class LocalizedText(
    val text: Map<String, String>  // languageCode -> text
)

fun LocalizedText.resolve(context: Context): String {
    val locale = context.resources.configuration.locales[0]
    // 1. 精确匹配（zh-CN）
    text[locale.toLanguageTag()]?.let { return it }
    // 2. 语言匹配（zh）
    text[locale.language]?.let { return it }
    // 3. 默认语言
    text["en"]?.let { return it }
    // 4. 任意可用
    return text.values.firstOrNull() ?: ""
}
```

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

- [Android 官方文档 - 字符串资源](https://developer.android.com/guide/topics/resources/string-resource)
- [Android 官方文档 - 复数资源](https://developer.android.com/guide/topics/resources/string-resource#Plurals)
- [Unicode CLDR - 复数规则](https://cldr.unicode.org/index/cldr-spec/plural-rules)
- [Android Lint 检查列表](https://googlesamples.github.io/android-custom-lint-rules/checks/index.md.html)
- [Android 官方文档 - 本地化检查清单](https://developer.android.com/guide/topics/resources/localization#checklist)

---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Android 测试基础、CI/CD 基础
关联模块: 01-国际化internationalization/02-字符串与翻译管理string-and-translation-management, 01-国际化internationalization/04-RTL布局适配rtl-layout-adaptation
---

# 多语言测试与质量保障

## Pseudolocale 测试

### en-XA（文本扩展伪语言）

`en-XA` 是 Android 提供的伪语言，将英文文本替换为**带重音符号的加长版本**，同时保持可读性。用途：

- 检测硬编码字符串（硬编码的文本不会被转换）
- 发现因文本过长导致的 UI 截断/溢出问题（转换后文本约长 30-50%）
- 暴露未正确使用资源引用的地方

```
原文: "Settings"
en-XA: "[Šéţţîñĝš one two]"

原文: "Log In"
en-XA: "[Ļöĝ Îñ one]"
```

> 如果某段文字在 en-XA 下仍然显示正常英文，说明它是硬编码的。

### ar-XB（RTL 伪语言）

`ar-XB` 将英文文本反转并添加 RTL 标记，同时切换布局方向为 RTL。用途：

- 检测 RTL 布局问题（镜像遗漏、方向属性错误）
- 在不安装阿拉伯语的情况下测试 RTL 效果
- 保持文本可辨认，方便定位问题

```
原文: "Settings"
ar-XB: "‎‏‎‎‎‎‎‏‏‎‎‏sgnitteS‏‎‎‎‎‎‏‏‎‎‎"
```

### 启用 Pseudolocale

**步骤 1**：在 `build.gradle.kts` 中启用：

```kotlin
android {
    buildTypes {
        debug {
            isPseudoLocalesEnabled = true
        }
    }
}
```

**步骤 2**：在设备/模拟器上选择伪语言：

设置 > 系统 > 语言 > 添加语言 > 选择 "English (XA)" 或 "AR (XB)"

> 伪语言仅在 `isPseudoLocalesEnabled = true` 的构建中可用，Release 版本不会包含。

## Android Lint i18n 规则

### 内置 Lint 规则

| 规则 ID | 默认级别 | 说明 |
|---------|:-------:|------|
| `HardcodedText` | Warning | 布局 XML 中直接写了文本字符串 |
| `SetTextI18n` | Warning | 代码中对 `setText()` 传入了硬编码字符串 |
| `MissingTranslation` | Error | 某语言的 strings.xml 缺少默认文件中的 key |
| `ExtraTranslation` | Error | 某语言的 strings.xml 包含默认文件中不存在的 key |
| `RtlEnabled` | Warning | 未声明 `supportsRtl="true"` |
| `RtlHardcoded` | Warning | 使用了 `left/right` 而非 `start/end` |
| `RtlCompat` | Warning | 使用了 `start/end` 但缺少 `left/right` 兼容属性 |
| `RtlSymmetry` | Warning | `start/end` 属性不对称（设了 start 没设 end） |
| `StringFormatInvalid` | Error | 格式化字符串的参数类型/数量不匹配 |
| `StringFormatMatches` | Error | 各语言间格式化参数不一致 |
| `PluralsCandidate` | Warning | 可能应该使用 plurals 而非 string |

### Lint 规则严格化配置

在 `lint.xml` 中将 i18n 相关警告升级为错误，阻止 CI 构建通过：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lint>
    <!-- 硬编码文本：升级为 error -->
    <issue id="HardcodedText" severity="error" />
    <issue id="SetTextI18n" severity="error" />

    <!-- 翻译完整性：保持 error -->
    <issue id="MissingTranslation" severity="error" />
    <issue id="ExtraTranslation" severity="error" />

    <!-- RTL 相关：升级为 error -->
    <issue id="RtlHardcoded" severity="error" />
    <issue id="RtlEnabled" severity="error" />

    <!-- 格式化字符串：保持 error -->
    <issue id="StringFormatInvalid" severity="error" />
    <issue id="StringFormatMatches" severity="error" />

    <!-- 对测试代码宽松处理 -->
    <issue id="HardcodedText">
        <ignore path="**/test/**" />
        <ignore path="**/androidTest/**" />
    </issue>
</lint>
```

### 自定义 Lint 规则

编写自定义 Lint 检查，覆盖标准规则未涵盖的场景：

```kotlin
// 检测 String.format 未使用位置参数
class PositionalFormatArgDetector : Detector(), Detector.XmlScanner {
    override fun getApplicableElements() = listOf("string")

    override fun visitElement(context: XmlContext, element: Element) {
        val text = element.textContent ?: return
        // 检查是否使用了 %s/%d 而非 %1$s/%1$d
        val nonPositional = Regex("%[^%\\d]").findAll(text)
        if (nonPositional.any()) {
            context.report(
                ISSUE,
                element,
                context.getLocation(element),
                "Use positional format arguments (%1\$s) instead of %s for i18n"
            )
        }
    }

    companion object {
        val ISSUE = Issue.create(
            id = "NonPositionalFormatArg",
            briefDescription = "Non-positional format argument",
            explanation = "Format strings should use positional arguments " +
                "(%1\$s, %2\$d) to allow translators to reorder arguments.",
            category = Category.I18N,
            severity = Severity.WARNING,
            implementation = Implementation(
                PositionalFormatArgDetector::class.java,
                Scope.RESOURCE_FILE_SCOPE
            )
        )
    }
}
```

## UI 自动化测试

### 多语言截图测试

使用截图测试框架自动生成各语言的 UI 截图，便于视觉回归检测：

**Paparazzi**（无需设备/模拟器）：

```kotlin
class LoginScreenTest {
    @get:Rule
    val paparazzi = Paparazzi(
        deviceConfig = DeviceConfig.PIXEL_6
    )

    @Test
    fun loginScreen_english() {
        paparazzi.snapshot {
            CompositionLocalProvider(
                LocalConfiguration provides Configuration().apply {
                    setLocale(Locale("en"))
                }
            ) {
                LoginScreen()
            }
        }
    }

    @Test
    fun loginScreen_arabic_rtl() {
        paparazzi.snapshot {
            CompositionLocalProvider(
                LocalLayoutDirection provides LayoutDirection.Rtl,
                LocalConfiguration provides Configuration().apply {
                    setLocale(Locale("ar"))
                }
            ) {
                LoginScreen()
            }
        }
    }
}
```

**Roborazzi**（Robolectric 环境）：

```kotlin
@RunWith(RobolectricTestRunner::class)
@GraphicsMode(GraphicsMode.Mode.NATIVE)
class ScreenshotTest {
    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun settingsScreen_multiLocale() {
        listOf("en", "zh", "ar", "ja").forEach { lang ->
            val locale = Locale(lang)
            Locale.setDefault(locale)

            composeTestRule.setContent {
                MyAppTheme { SettingsScreen() }
            }

            composeTestRule.onRoot()
                .captureRoboImage("screenshots/settings_$lang.png")
        }
    }
}
```

### 文本截断/溢出检测

自动检测 UI 元素是否因翻译过长导致文本被截断：

```kotlin
@Test
fun checkNoTextTruncation() {
    composeTestRule.setContent {
        MyAppTheme { HomeScreen() }
    }

    // 检查所有 Text 节点是否有溢出
    composeTestRule.onAllNodes(hasText(""))
        .fetchSemanticsNodes()
        .forEach { node ->
            val textLayoutResult = node.config
                .getOrNull(SemanticsProperties.TextLayoutResult)
            textLayoutResult?.forEach { result ->
                assertFalse(
                    "Text overflow detected: ${result.layoutInput.text}",
                    result.hasVisualOverflow
                )
            }
        }
}
```

### 各语言 UI 回归对比

将多语言截图纳入 CI，每次 PR 自动对比差异：

```yaml
# .github/workflows/screenshot-test.yml
name: Screenshot Tests
on: [pull_request]

jobs:
  screenshot:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run screenshot tests
        run: ./gradlew verifyPaparazziDebug
      - name: Upload diff
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: screenshot-diffs
          path: '**/build/paparazzi/failures/'
```

## Espresso / Compose Testing

### Espresso 中切换 Locale

```kotlin
@RunWith(AndroidJUnit4::class)
class I18nEspressoTest {

    @get:Rule
    val activityRule = ActivityScenarioRule(MainActivity::class.java)

    @Test
    fun verifyChineseTranslation() {
        // 切换到中文
        setLocale(Locale("zh", "CN"))

        activityRule.scenario.recreate()

        onView(withId(R.id.title))
            .check(matches(withText("设置")))
    }

    private fun setLocale(locale: Locale) {
        Locale.setDefault(locale)
        val config = Configuration()
        config.setLocale(locale)
        InstrumentationRegistry.getInstrumentation()
            .targetContext.resources
            .updateConfiguration(config, null)
    }
}
```

### Compose Testing 中的 Locale

```kotlin
class ComposeI18nTest {
    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun verifyArabicLayout() {
        composeTestRule.setContent {
            CompositionLocalProvider(
                LocalLayoutDirection provides LayoutDirection.Rtl
            ) {
                MyAppTheme {
                    NavigationBar()
                }
            }
        }

        // 验证 RTL 布局中的元素位置
        composeTestRule.onNodeWithTag("nav_home")
            .assertExists()
    }
}
```

### 多语言 UI 断言

```kotlin
@Test
fun verifyAllLanguagesHaveTranslation() {
    val locales = listOf(
        Locale("en"), Locale("zh", "CN"), Locale("ar"), Locale("ja")
    )

    locales.forEach { locale ->
        val context = InstrumentationRegistry.getInstrumentation().targetContext
        val config = Configuration(context.resources.configuration)
        config.setLocale(locale)
        val localizedContext = context.createConfigurationContext(config)

        val text = localizedContext.getString(R.string.app_name)
        assertTrue(
            "app_name should not be empty for ${locale.toLanguageTag()}",
            text.isNotBlank()
        )
    }
}
```

## CI 质量门禁

### 翻译完整性检查

```yaml
# CI step
- name: Check translation completeness
  run: |
    ./scripts/check_missing_translations.sh
    if [ $? -ne 0 ]; then
      echo "❌ Translation check failed"
      exit 1
    fi
```

### 格式化一致性检查

```yaml
- name: Check format specifiers
  run: |
    python3 scripts/check_format_specifiers.py
    if [ $? -ne 0 ]; then
      echo "❌ Format specifier mismatch detected"
      exit 1
    fi
```

### RTL 布局检查

```yaml
- name: Lint RTL check
  run: |
    ./gradlew lintDebug -Dlint.check=RtlHardcoded,RtlEnabled,RtlCompat,RtlSymmetry
    # 检查 lint 报告中是否有 RTL 相关 error
```

### 截图回归检查

```yaml
- name: Screenshot regression
  run: |
    ./gradlew verifyPaparazziDebug
  # 失败时上传 diff 截图供人工检查
```

## 手动测试 Checklist

### 语言切换测试项

| 测试项 | 预期结果 | ✅/❌ |
|--------|----------|:----:|
| 应用内切换语言后 UI 立即更新 | 所有文本切换为目标语言 | |
| 切换语言后杀掉应用重启 | 语言设置保持不变 | |
| 返回栈中的页面语言一致 | 按返回键，之前的页面也是新语言 | |
| WebView 页面语言同步 | WebView 内容与应用语言一致 | |
| 通知/Widget 语言同步 | 通知和桌面小组件显示新语言 | |
| "跟随系统"选项 | 切换系统语言后应用自动更新 | |

### RTL 测试项

| 测试项 | 预期结果 | ✅/❌ |
|--------|----------|:----:|
| 整体布局镜像 | 导航栏、列表、表单等水平翻转 | |
| 文本对齐方向 | 文本从右侧开始 | |
| 图标方向 | 箭头等方向图标正确镜像 | |
| 手势方向 | 滑动返回为从右向左 | |
| 进度条方向 | 从右向左填充 | |
| 不应镜像的元素 | 时钟、播放按钮等保持不变 | |
| 混合文本 | 阿拉伯文中嵌入英文数字显示正确 | |

### 边界场景测试

| 测试项 | 预期结果 | ✅/❌ |
|--------|----------|:----:|
| 极长翻译文本（如德语） | UI 不溢出，文本正确换行或省略 | |
| 缺失翻译 | Fallback 到默认语言，不崩溃 | |
| 特殊字符（emoji、ZWJ） | 正确显示，不乱码 | |
| 复数规则验证（0/1/2/多） | 各数量显示正确的复数形式 | |
| 空字符串 | 不显示空白区域，有合理的默认值 | |

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

- [Android 官方文档 - 使用伪语言环境测试应用](https://developer.android.com/guide/topics/resources/pseudolocales)
- [Android Lint 检查完整列表](https://googlesamples.github.io/android-custom-lint-rules/checks/index.md.html)
- [Paparazzi - GitHub](https://github.com/cashapp/paparazzi)
- [Roborazzi - GitHub](https://github.com/takahirom/roborazzi)
- [Android 官方文档 - 编写自定义 Lint 规则](https://developer.android.com/studio/write/lint)
- [Espresso 测试文档](https://developer.android.com/training/testing/espresso)
- [Compose Testing 文档](https://developer.android.com/develop/ui/compose/testing)

---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 待补充
前置知识: Android 测试基础、CI/CD 基础
关联模块: 01-国际化internationalization/02-字符串与翻译管理string-and-translation-management, 01-国际化internationalization/04-RTL布局适配rtl-layout-adaptation
---

# 多语言测试与质量保障

## Pseudolocale 测试

### en-XA（文本扩展伪语言）

<!-- 检测硬编码字符串、文本截断、UI 溢出 -->

### ar-XB（RTL 伪语言）

<!-- 检测 RTL 布局问题、镜像遗漏 -->

### 启用 Pseudolocale

<!-- build.gradle 配置 + 设备开发者选项 -->

## Android Lint i18n 规则

### 内置 Lint 规则

<!-- 表格：MissingTranslation, HardcodedText, RtlEnabled, RtlHardcoded, RtlCompat 等 -->

### Lint 规则严格化配置

<!-- lint.xml 中将 i18n 相关 warning 升级为 error -->

### 自定义 Lint 规则

<!-- 编写自定义 Lint 检查（如检测 String.format 未使用位置参数） -->

## UI 自动化测试

### 多语言截图测试

<!-- Screenshot Testing 框架：Paparazzi / Shot / Roborazzi -->

### 文本截断/溢出检测

<!-- 自动检测 UI 元素文本是否被截断 -->

### 各语言 UI 回归对比

<!-- 截图对比发现布局异常 -->

## Espresso / Compose Testing

### Espresso 中切换 Locale

<!-- 测试中临时切换语言环境 -->

### Compose Testing 中的 Locale

<!-- ComposeTestRule 中设置 Locale -->

### 多语言 UI 断言

<!-- 验证 UI 元素显示正确的翻译文本 -->

## CI 质量门禁

### 翻译完整性检查

<!-- CI 中检查所有语言文件是否覆盖全部 key -->

### 格式化一致性检查

<!-- CI 中验证格式符在各语言间的一致性 -->

### RTL 布局检查

<!-- CI 中检查 left/right 是否已替换为 start/end -->

### 截图回归检查

<!-- CI 中自动进行多语言截图对比 -->

## 手动测试 Checklist

### 语言切换测试项

<!-- 切换后 UI 刷新、状态保持、WebView 同步等 -->

### RTL 测试项

<!-- 布局镜像、图标方向、手势方向等 -->

### 边界场景测试

<!-- 极长翻译文本、缺失翻译 fallback、特殊字符等 -->

## 踩坑记录

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

<!-- Android 官方文档：Lint、Pseudolocale、测试 -->

---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 待补充
前置知识: Jetpack Compose 基础、Kotlin 基础
关联模块: 01-国际化internationalization/02-字符串与翻译管理string-and-translation-management, 01-国际化internationalization/04-RTL布局适配rtl-layout-adaptation
---

# Compose 国际化实践

## Compose 中的字符串资源

### stringResource()

<!-- 基本用法、带参数的格式化 -->

### pluralStringResource()

<!-- 复数字符串在 Compose 中的使用 -->

### stringArrayResource()

<!-- 字符串数组资源 -->

### 自定义资源访问扩展

<!-- 封装常用的资源访问工具函数 -->

## CompositionLocal 与 Locale

### LocalConfiguration

<!-- 读取当前 Configuration 中的 Locale 信息 -->

### LocalContext

<!-- 通过 Context 获取资源 -->

### 自定义 CompositionLocal 传递语言状态

<!-- 在 Compose 树中传递自定义的语言状态 -->

## Compose 中的 RTL 支持

### LayoutDirection

<!-- CompositionLocalLayoutDirection 的使用 -->

### CompositionLocalLayoutDirection

<!-- 读取和覆盖布局方向 -->

### Row / Column 在 RTL 下的行为

<!-- Compose 布局组件对 RTL 的原生支持 -->

### Modifier 的方向感知

<!-- Modifier.padding 中 start/end 的行为 -->

## 动态语言切换与 Compose

### 触发 Recomposition

<!-- 语言切换后如何触发 Compose UI 刷新 -->

### 与 Activity recreate 的配合

<!-- Compose Activity 重建时的状态恢复 -->

### 纯 Compose 应用的语言切换方案

<!-- 无传统 View 的全 Compose 应用如何切换语言 -->

## Compose Preview 多语言预览

### @Preview locale 参数

<!-- @Preview(locale = "ar") 预览不同语言效果 -->

### 多语言 Preview 批量生成

<!-- 使用 @PreviewParameterProvider 批量生成多语言预览 -->

### Preview 中的 RTL 验证

<!-- 在 Preview 中检查 RTL 布局 -->

## Compose + Material3 国际化

### Material3 组件的 i18n 支持

<!-- TopAppBar, NavigationBar 等组件的 RTL 行为 -->

### 主题中的文字排版适配

<!-- Typography 在不同语言下的调整 -->

## View 与 Compose 混用场景

### AndroidView 中的语言一致性

<!-- 在 Compose 中嵌入传统 View 时的语言同步 -->

### ComposeView 中的语言一致性

<!-- 在传统 View 中嵌入 Compose 时的语言同步 -->

## 踩坑记录

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

<!-- Jetpack Compose 官方文档、Material3 文档 -->

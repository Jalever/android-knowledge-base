---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 待补充
前置知识: Android 调试基础、ADB 命令基础
关联模块: 01-国际化internationalization/03-动态语言切换dynamic-language-switching, 01-国际化internationalization/04-RTL布局适配rtl-layout-adaptation
---

# 调试与常见问题排查

## 调试工具

### ADB 切换设备语言

<!-- adb shell 命令快速切换 Locale，无需手动进入设置 -->

### Layout Inspector 查看布局方向

<!-- 使用 Layout Inspector 检查 RTL 布局是否生效 -->

### Android Studio Translations Editor

<!-- 可视化管理多语言翻译的内置工具 -->

### Locale 信息查看

<!-- 运行时获取和打印当前 Locale 信息的调试方法 -->

## 常见问题：语言切换

### WebView 语言不同步

<!-- 问题现象、原因分析、Accept-Language Header 解决方案 -->

### 第三方 SDK 语言不跟随

<!-- SDK 初始化缓存语言的问题、重新初始化或调用 SDK 语言 API -->

### Activity recreate 状态丢失

<!-- ViewModel / SavedState 未正确保存导致数据丢失 -->

### 切换语言后返回栈页面语言不一致

<!-- 栈内 Activity 未全部 recreate 的问题与解决 -->

## 常见问题：资源加载

### strings.xml 缺失 key 导致 Crash

<!-- MissingFormatArgumentException 或资源未找到异常 -->

### 资源 Fallback 到非预期语言

<!-- Fallback 算法未按预期工作的排查方法 -->

### App Bundle 语言包未下载

<!-- 用户设备未下载对应语言的 split APK -->

### 资源缓存导致翻译不更新

<!-- ResourcesManager 缓存导致新翻译不生效 -->

## 常见问题：RTL 布局

### 部分页面未镜像

<!-- 遗漏 start/end 替换、自定义 View 未适配 -->

### 数字/标点在 RTL 文本中位置错乱

<!-- Unicode BiDi 控制字符的使用 -->

### 动画方向未适配

<!-- 滑动动画、转场动画在 RTL 下的方向问题 -->

## 常见问题：格式化

### 复数规则错误

<!-- 阿拉伯语等复杂语言的复数形式遗漏 -->

### 日期/货币格式不符合地区习惯

<!-- 未使用 Locale-aware 的格式化 API -->

### 排序结果不符合语言习惯

<!-- 未使用 Collator 进行本地化排序 -->

## 特定设备/ROM 适配

### 小米 MIUI 的 Locale 行为

<!-- MIUI 特有的语言处理逻辑 -->

### OPPO / vivo ColorOS / OriginOS

<!-- 厂商 ROM 对语言设置的特殊处理 -->

### 鸿蒙 HarmonyOS 兼容性

<!-- 鸿蒙系统的 Locale 机制差异 -->

## 排查流程总结

### i18n 问题排查决策树

<!-- Mermaid flowchart：根据症状定位问题类型的排查流程图 -->

### 日志关键字速查

<!-- 表格：常见 i18n 相关错误的 Logcat 关键字 -->

## 踩坑记录

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

<!-- Android 官方文档、Stack Overflow 高频 i18n 问题链接 -->

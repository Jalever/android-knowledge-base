---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: Android 布局基础、ConstraintLayout 基础
关联模块: 01-国际化internationalization/00-概要overview, 01-国际化internationalization/02-字符串与翻译管理string-and-translation-management
---

# RTL 布局适配

## RTL 基本概念

### RTL 语言与使用地区

RTL（Right-to-Left）指从右向左书写的语言体系。全球约有 12 亿人使用 RTL 语言：

| 语言 | 代码 | 使用地区 | 使用人数 |
|------|------|----------|----------|
| 阿拉伯语 | `ar` | 中东、北非（22 个国家） | ~4.2 亿 |
| 希伯来语 | `he` / `iw` | 以色列 | ~0.9 亿 |
| 波斯语（法尔西语） | `fa` | 伊朗、阿富汗 | ~1.1 亿 |
| 乌尔都语 | `ur` | 巴基斯坦、印度 | ~2.3 亿 |
| 维吾尔语 | `ug` | 中国新疆 | ~1200 万 |

### RTL 布局的视觉效果

RTL 布局下，整个 UI 会水平镜像翻转：

```
LTR 布局（如中文、英文）           RTL 布局（如阿拉伯语）
┌─────────────────────────┐      ┌─────────────────────────┐
│ [←返回]   标题    [菜单]│      │[菜单]    العنوان   [返回→]│
├─────────────────────────┤      ├─────────────────────────┤
│ [头像] 用户名            │      │            اسم المستخدم [头像]│
│        消息内容...       │      │        ...محتوى الرسالة │
├─────────────────────────┤      ├─────────────────────────┤
│ ✅ 已读   14:30         │      │         14:30   已读 ✅ │
└─────────────────────────┘      └─────────────────────────┘
```

## 基础配置

### supportsRtl 声明

在 `AndroidManifest.xml` 中启用 RTL 支持：

```xml
<application
    android:supportsRtl="true"
    ...>
```

> 当 `targetSdkVersion >= 17`（Android 4.2）时，设置 `supportsRtl="true"` 后系统会自动将 `left/right` 属性映射为 `start/end`。但仍建议**手动替换**所有 `left/right` 为 `start/end`。

### targetSdkVersion 的影响

| targetSdkVersion | RTL 行为 |
|:----------------:|----------|
| < 17 | 完全忽略 RTL，即使声明了 `supportsRtl` |
| >= 17 | 启用 RTL 支持，`start/end` 属性生效 |
| >= 28 | 部分系统组件强制支持 RTL |

## 布局属性适配

### start/end 替代 left/right

这是 RTL 适配中**最关键也最大量**的修改。所有方向相关的布局属性都需要替换：

| 旧属性（方向固定） | 新属性（方向感知） |
|:------------------:|:------------------:|
| `layout_marginLeft` | `layout_marginStart` |
| `layout_marginRight` | `layout_marginEnd` |
| `paddingLeft` | `paddingStart` |
| `paddingRight` | `paddingEnd` |
| `layout_alignParentLeft` | `layout_alignParentStart` |
| `layout_alignParentRight` | `layout_alignParentEnd` |
| `layout_toLeftOf` | `layout_toStartOf` |
| `layout_toRightOf` | `layout_toEndOf` |
| `drawableLeft` | `drawableStart` |
| `drawableRight` | `drawableEnd` |

```xml
<!-- ❌ 方向固定 -->
<TextView
    android:layout_marginLeft="16dp"
    android:paddingRight="8dp"
    android:drawableLeft="@drawable/ic_arrow" />

<!-- ✅ 方向感知 -->
<TextView
    android:layout_marginStart="16dp"
    android:paddingEnd="8dp"
    android:drawableStart="@drawable/ic_arrow" />
```

> **快捷操作**：Android Studio 提供 Refactor > Add RTL Support 功能，可批量替换。

### Gravity 与 TextAlignment

```kotlin
// ❌ 方向固定
textView.gravity = Gravity.LEFT

// ✅ 方向感知
textView.gravity = Gravity.START
```

```xml
<!-- 文本对齐方式 -->
<TextView
    android:textAlignment="viewStart"
    android:gravity="start" />
```

| Gravity 值 | 行为 |
|------------|------|
| `START` | LTR 时靠左，RTL 时靠右 |
| `END` | LTR 时靠右，RTL 时靠左 |
| `LEFT` | 始终靠左（不受 RTL 影响） |
| `RIGHT` | 始终靠右（不受 RTL 影响） |

### layoutDirection 属性

在特定场景下需要强制指定布局方向，不随系统 Locale 变化：

```xml
<!-- 强制 LTR（如电话号码输入框） -->
<EditText
    android:layoutDirection="ltr"
    android:textDirection="ltr" />

<!-- 强制 RTL -->
<LinearLayout android:layoutDirection="rtl" />

<!-- 跟随 Locale（默认行为） -->
<LinearLayout android:layoutDirection="locale" />
```

## 各布局组件的 RTL 行为

### ConstraintLayout

ConstraintLayout 天然支持 RTL，使用 `start/end` 约束即可：

```xml
<TextView
    android:id="@+id/label"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toStartOf="@id/value" />

<TextView
    android:id="@+id/value"
    app:layout_constraintEnd_toEndOf="parent" />
```

> `layout_constraintLeft_toLeftOf` 等 `Left/Right` 约束**不会**自动镜像，必须使用 `Start/End`。

### LinearLayout

水平方向的 `LinearLayout` 在 RTL 下子 View 排列方向自动反转：

```xml
<!-- LTR: A → B → C -->
<!-- RTL: C ← B ← A -->
<LinearLayout android:orientation="horizontal">
    <View android:id="@+id/a" />
    <View android:id="@+id/b" />
    <View android:id="@+id/c" />
</LinearLayout>
```

### RecyclerView

`LinearLayoutManager` 在 RTL 下自动调整滚动方向：

```kotlin
// 水平 RecyclerView 在 RTL 下自动从右向左排列
val layoutManager = LinearLayoutManager(context, RecyclerView.HORIZONTAL, false)
recyclerView.layoutManager = layoutManager

// 如果需要手动控制方向
layoutManager.isReverseLayout = isRtl
```

> `GridLayoutManager` 和 `StaggeredGridLayoutManager` 也自动支持 RTL。

### ViewPager2

ViewPager2 原生支持 RTL：在 RTL 模式下，第一页在最右边，向左滑动查看后续页面。

```kotlin
// ViewPager2 自动处理 RTL，无需额外代码
// 但如果使用自定义 PageTransformer，需要注意方向
viewPager.setPageTransformer { page, position ->
    val isRtl = viewPager.layoutDirection == View.LAYOUT_DIRECTION_RTL
    val adjustedPosition = if (isRtl) -position else position
    // 使用 adjustedPosition 做动画
}
```

## 双向文本（BiDi）处理

### Unicode BiDi 算法

当一段文本同时包含 LTR 和 RTL 字符时，Unicode 双向算法（BiDi Algorithm）决定各字符的显示顺序。常见场景：

```
阿拉伯文中嵌入英文：  "مرحبا Hello World مرحبا"
英文中嵌入阿拉伯文：  "Welcome مرحبا to our app"
```

### BidiFormatter

Android 提供 `BidiFormatter` 处理混合方向文本，避免显示异常：

```kotlin
val formatter = BidiFormatter.getInstance()

// 在 RTL 上下文中嵌入 LTR 文本
val text = formatter.unicodeWrap("John Smith")  // 自动添加 Unicode 方向标记

// 在格式化字符串中使用
val message = getString(
    R.string.welcome_user,
    formatter.unicodeWrap(userName)
)
```

### 常见 BiDi 显示异常

| 场景 | 问题 | 解决方案 |
|------|------|----------|
| 电话号码 `+86-138xxxx` | 在 RTL 中 `+` 和 `-` 位置错乱 | 用 `\u200E`（LTR mark）包裹 |
| URL `https://example.com` | 在 RTL 文本中方向混乱 | 使用 `BidiFormatter.unicodeWrap()` |
| 文件路径 `/sdcard/photo.jpg` | 斜杠和点的位置异常 | 强制 LTR：`\u202A...\u202C` |
| 混合数字 `100 - 200` | 连字符被解释为减号，位置不对 | 使用 `BidiFormatter` |

## 图标与图片适配

### autoMirrored 属性

VectorDrawable 可设置 `autoMirrored` 在 RTL 下自动水平翻转：

```xml
<!-- res/drawable/ic_arrow_forward.xml -->
<vector
    android:autoMirrored="true"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24">
    <path android:pathData="M12,4l-1.41,1.41L16.17,11H4v2h12.17l-5.58,5.59L12,20l8,-8z" />
</vector>
```

### drawable-ldrtl 资源目录

当自动镜像不适用时，可以为 RTL 提供单独的图片资源：

```
res/
├── drawable/           # LTR 默认图片
│   └── ic_progress.png
└── drawable-ldrtl/     # RTL 专用图片
    └── ic_progress.png
```

### 不应镜像的图标

并非所有图标都应该在 RTL 下镜像。以下类型**不应镜像**：

| 类型 | 示例 | 原因 |
|------|------|------|
| 时钟 | 🕐 | 指针方向是物理事实 |
| 媒体播放控制 | ▶ ⏸ ⏹ | 播放/暂停是通用符号 |
| 图表/数据 | 📊 | 数据趋势方向不应改变 |
| 品牌 Logo | | 品牌标识不应镜像 |
| 对号/叉号 | ✓ ✗ | 通用符号 |
| 物理方向 | 🧭 | 反映真实方向 |

**应该镜像**的图标：

| 类型 | 示例 | 原因 |
|------|------|------|
| 导航箭头 | ← → | 表示阅读/浏览方向 |
| 列表缩进 | 📝 | 文本方向相关 |
| 聊天气泡尾巴 | 💬 | 指向发送者位置 |
| 进度指示 | ▸▸▸ | 表示前进方向 |

## 自定义 View 的 RTL 适配

### Canvas 绘制方向

自定义 View 的 `onDraw` 中需要根据布局方向调整绘制逻辑：

```kotlin
override fun onDraw(canvas: Canvas) {
    super.onDraw(canvas)
    val isRtl = layoutDirection == View.LAYOUT_DIRECTION_RTL

    if (isRtl) {
        // 方案 1：翻转 Canvas
        canvas.save()
        canvas.scale(-1f, 1f, width / 2f, height / 2f)
        drawContent(canvas)
        canvas.restore()
    } else {
        drawContent(canvas)
    }
}

// 方案 2：根据方向调整坐标
private fun drawProgressBar(canvas: Canvas) {
    val isRtl = layoutDirection == View.LAYOUT_DIRECTION_RTL
    val startX = if (isRtl) width.toFloat() else 0f
    val endX = if (isRtl) width * (1 - progress) else width * progress
    canvas.drawRect(startX, 0f, endX, height.toFloat(), paint)
}
```

### 手势处理

滑动手势在 RTL 下可能需要反转方向：

```kotlin
override fun onTouchEvent(event: MotionEvent): Boolean {
    val isRtl = layoutDirection == View.LAYOUT_DIRECTION_RTL
    when (event.action) {
        MotionEvent.ACTION_MOVE -> {
            val dx = event.x - lastX
            val logicalDx = if (isRtl) -dx else dx
            // 使用 logicalDx 处理滑动
        }
    }
    return super.onTouchEvent(event)
}
```

## 测试与验证

### 开发者选项"强制 RTL 布局"

无需安装阿拉伯语输入法，即可快速测试 RTL 布局效果：

**路径**：设置 > 开发者选项 > 强制使用从右到左的布局方向

> 该选项会将所有应用的布局方向强制设为 RTL，方便快速检查整体布局是否正确镜像。

### Pseudolocale ar-XB

Android 提供的 RTL 伪语言，在保持英文可读的同时模拟 RTL 布局：

```kotlin
// build.gradle.kts
android {
    buildTypes {
        debug {
            isPseudoLocalesEnabled = true
        }
    }
}
```

启用后在设备语言设置中可以选择 "AR (XB) Pseudo..."，所有文本会反转但仍可辨认，方便发现 RTL 问题。

### Layout Inspector 检查

使用 Android Studio 的 Layout Inspector 验证布局方向：

1. 运行应用并切换到 RTL 语言
2. 打开 Layout Inspector（Tools > Layout Inspector）
3. 检查各 View 的 `layoutDirection` 属性
4. 确认 `margin`、`padding` 的 `start/end` 值是否正确

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

- [Android 官方文档 - 支持不同语言（RTL）](https://developer.android.com/training/basics/supporting-devices/languages#RTL)
- [Material Design - 双向性](https://m3.material.io/foundations/layout/understanding-layout/bidirectionality)
- [Unicode BiDi 算法](https://unicode.org/reports/tr9/)
- [BidiFormatter API](https://developer.android.com/reference/android/text/BidiFormatter)
- [Android 官方博客 - RTL Support](https://android-developers.googleblog.com/2013/03/native-rtl-support-in-android-42.html)

---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: 01-蓝牙基础知识bluetooth-fundamentals
关联模块: 10-连接稳定性与重连connection-stability
---

# 调试与问题排查

蓝牙问题的排查因涉及射频层、协议栈、系统层、应用层等多个环节而格外复杂。本文整理了核心调试工具、日志分析方法和常见问题速查表。

## HCI Snoop Log

HCI Snoop Log 记录了 Android 蓝牙协议栈的所有 HCI（Host Controller Interface）数据包，是分析底层蓝牙问题的终极工具。

### 开启方式（开发者选项）

1. 进入"设置 → 关于手机"，连续点击"版本号" 7 次开启开发者模式
2. 进入"设置 → 系统 → 开发者选项"
3. 找到"蓝牙 HCI 信息收集日志"（Enable Bluetooth HCI snoop log），开启
4. 重启蓝牙（关闭后重新开启）使设置生效
5. 复现问题
6. 关闭 HCI 日志采集

### 日志文件位置与导出

```bash
# 常见日志路径（因 ROM 而异）
/data/misc/bluetooth/logs/btsnoop_hci.log
/sdcard/btsnoop_hci.log

# 通过 adb 导出
adb pull /data/misc/bluetooth/logs/btsnoop_hci.log ./
# 或使用 bug report
adb bugreport bugreport.zip
# bug report 中包含 btsnoop_hci.log
```

部分手机（如 Samsung）在开发者选项中有专门的"蓝牙日志导出"入口。

### 使用 Wireshark 分析 HCI 日志

1. 安装 [Wireshark](https://www.wireshark.org/)
2. 打开 btsnoop_hci.log 文件（Wireshark 原生支持此格式）
3. 使用过滤器定位问题：

```
# 常用 Wireshark 过滤表达式

# 只看 GATT 操作
btatt

# 只看特定设备（按 BT 地址过滤）
bluetooth.addr == aa:bb:cc:dd:ee:ff

# 只看连接事件
btle.advertising_header || btle.data_header

# 只看 ATT 写操作
btatt.opcode == 0x12 || btatt.opcode == 0x52

# 只看 ATT 通知
btatt.opcode == 0x1b

# 只看错误响应
btatt.opcode == 0x01

# 只看连接参数更新
btl2cap.cid == 0x0005
```

### 常见协议包解读

| 操作 | ATT Opcode | 含义 |
|------|-----------|------|
| Read Request | 0x0A | Client 读取 Characteristic |
| Read Response | 0x0B | Server 返回读取结果 |
| Write Request | 0x12 | Client 写入（需确认） |
| Write Response | 0x13 | Server 确认写入成功 |
| Write Command | 0x52 | Client 写入（无需确认） |
| Notification | 0x1B | Server 主动推送通知 |
| Indication | 0x1D | Server 主动推送指示 |
| Confirmation | 0x1E | Client 确认收到 Indication |
| Error Response | 0x01 | 操作失败错误 |

**ATT Error Code 速查：**

| Error Code | 含义 |
|-----------|------|
| 0x01 | Invalid Handle |
| 0x02 | Read Not Permitted |
| 0x03 | Write Not Permitted |
| 0x05 | Insufficient Authentication |
| 0x06 | Request Not Supported |
| 0x07 | Invalid Offset |
| 0x0D | Invalid Attribute Length |
| 0x0E | Insufficient Encryption |

## nRF Connect 工具

nRF Connect for Mobile（Nordic 出品）是 BLE 开发者必备的调试工具，免费且功能强大。

### 扫描与设备发现

- 列出周围所有 BLE 广播设备
- 显示设备名称、MAC 地址、RSSI、广播间隔
- 可按名称、RSSI、Service UUID 过滤
- 支持持续扫描和单次扫描

### 连接与服务浏览

- 点击设备即可连接，自动执行 Service Discovery
- 以树状结构展示 Service → Characteristic → Descriptor
- 显示每个 Characteristic 的 Properties（R/W/N/I）
- 显示 UUID 和 Handle 值

### Characteristic 读写测试

- 直接在 UI 上读取 Characteristic 值（Hex / Text / 自定义格式）
- 写入数据到 Characteristic（支持 Hex 输入）
- 一键开启/关闭 Notification/Indication
- 记录接收到的通知数据流

### 广播包分析

- 解析广播包的每个 AD Structure
- 显示 AD Type、数据长度、原始 Hex 和解析后的含义
- 区分 Advertising Data 和 Scan Response Data

### 日志导出与分享

- 完整的操作日志，包含每个 GATT 操作的请求/响应
- 可导出为文本文件分享给团队成员或设备端开发者
- 对于远程排查问题非常有价值

## Android Studio 调试

### Logcat TAG 过滤

蓝牙相关的系统日志 TAG：

#### bt_btif

底层蓝牙协议栈日志，包含连接/断连的底层原因：

```
adb logcat -s bt_btif
```

#### BluetoothGatt

GATT 操作日志（连接、服务发现、读写）：

```
adb logcat -s BluetoothGatt
```

#### BtGatt.GattService

GATT Service 层日志：

```
adb logcat -s BtGatt.GattService
```

#### BluetoothAdapter

蓝牙适配器状态变化：

```
adb logcat -s BluetoothAdapter
```

**综合过滤命令：**

```bash
adb logcat | grep -iE "bluetooth|bt_btif|btgatt|btif_|ble|BluetoothGatt|BluetoothAdapter"
```

### 蓝牙相关日志级别调整

可通过系统属性调整蓝牙日志的详细程度（需 root 或 userdebug 版本）：

```bash
# 开启详细蓝牙日志
adb shell setprop persist.bluetooth.btsnoopenable true
adb shell setprop persist.bluetooth.btsnooplogmode full

# 重启蓝牙使生效
adb shell settings put global bluetooth_disabled_profiles 0
```

## 系统级调试工具

### dumpsys bluetooth_manager

```bash
adb shell dumpsys bluetooth_manager
```

输出内容包括：
- 蓝牙开关状态
- 当前连接的所有设备
- 各 Profile（A2DP / HFP / GATT 等）的连接状态
- 最近的蓝牙操作日志
- 蓝牙栈版本信息

**关注字段：**

```bash
# 快速查看已连接设备
adb shell dumpsys bluetooth_manager | grep "connected"

# 查看 GATT 连接信息
adb shell dumpsys bluetooth_manager | grep -A 5 "GATT"
```

### adb shell 蓝牙相关命令

```bash
# 查看蓝牙适配器信息
adb shell settings get global bluetooth_on

# 查看已绑定设备
adb shell dumpsys bluetooth_manager | grep "bond"

# 查看蓝牙地址
adb shell settings get secure bluetooth_address
```

### 蓝牙状态信息查看

```kotlin
// 在应用内获取蓝牙诊断信息
fun getBluetoothDiagnostics(context: Context): Map<String, String> {
    val bluetoothManager = context.getSystemService(Context.BLUETOOTH_SERVICE) as BluetoothManager
    val adapter = bluetoothManager.adapter

    return mapOf(
        "Bluetooth Enabled" to "${adapter?.isEnabled}",
        "Adapter Name" to "${adapter?.name}",
        "Adapter Address" to "${adapter?.address}",
        "BLE Supported" to "${context.packageManager.hasSystemFeature(PackageManager.FEATURE_BLUETOOTH_LE)}",
        "LE 2M PHY" to "${adapter?.isLe2MPhySupported}",
        "LE Coded PHY" to "${adapter?.isLeCodedPhySupported}",
        "Multiple Advertisement" to "${adapter?.isMultipleAdvertisementSupported}",
        "Offloaded Scan Batching" to "${adapter?.isOffloadedScanBatchingSupported}",
        "Bonded Devices" to "${adapter?.bondedDevices?.size}",
        "Scan Mode" to "${adapter?.scanMode}"
    )
}
```

## 常见问题速查表

### 扫描不到设备

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 权限未授予 | 检查运行时权限 | 请求 BLUETOOTH_SCAN（API 31+）或 ACCESS_FINE_LOCATION |
| 位置服务未开启 | 检查 LocationManager | 引导用户开启位置服务（API 30-） |
| 蓝牙未开启 | 检查 adapter.isEnabled | 引导用户开启蓝牙 |
| 设备未在广播 | 用 nRF Connect 验证 | 检查设备端广播是否正常 |
| 未使用 ScanFilter | Logcat 检查扫描回调 | 添加 ScanFilter（后台扫描必需） |
| 扫描过于频繁 | 检查 onScanFailed 错误码 | 减少启停频率，间隔 >6 秒 |
| 设备使用随机地址 | HCI 日志分析 | 使用 Service UUID 过滤而非 MAC 地址 |

### 连接后立即断开（status 133）

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 之前的 GATT 未 close() | 检查代码逻辑 | 断连后始终调用 gatt.close() |
| 并发连接数超限 | dumpsys 查看连接数 | 减少并发连接或先断开旧连接 |
| 蓝牙缓存异常 | 换一台手机测试 | 调用 refreshDeviceCache() |
| 系统蓝牙栈异常 | 重启蓝牙测试 | 重启蓝牙或重启手机 |
| 设备不在范围 | 检查 RSSI | 确保设备在有效距离内 |

### GATT 操作返回失败

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 操作未队列化 | 检查是否等待回调 | 实现操作队列，串行执行 |
| 未发现服务 | 检查 discoverServices | 确保在 onServicesDiscovered 后操作 |
| Characteristic 不支持操作 | 检查 Properties | 确认 Characteristic 支持 Read/Write/Notify |
| 数据超过 MTU | 检查数据长度 | 协商 MTU 或分包 |
| 未授权 | 检查 ATT Error | 需要先配对或加密 |

### 通知/指示收不到数据

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 未写入 CCCD | 检查代码 | setCharacteristicNotification + 写入 CCCD |
| 只调了 setCharacteristic... | 检查是否写 Descriptor | 必须同时写入 CCCD Descriptor |
| 设备端未发送通知 | nRF Connect 验证 | 检查设备端通知逻辑 |
| 回调被覆盖 | 检查 GattCallback 注册 | 确保使用正确的 Callback 实例 |

### Android 12+ 权限导致崩溃

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 未请求新权限 | Logcat 看 SecurityException | 请求 BLUETOOTH_SCAN / BLUETOOTH_CONNECT |
| targetSdk 升到 31+ | 检查 build.gradle | 迁移权限模型 |
| 缺少 BLUETOOTH_CONNECT | 调用 getName() 等崩溃 | 获取设备名称前检查权限 |

### 蓝牙缓存导致服务发现异常

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 设备固件更新后 Service 变化 | 对比新旧 Service 列表 | 连接后调用 refreshDeviceCache() |
| 开发阶段频繁改 Service | nRF Connect 对比 | 清除手机蓝牙缓存（设置 → 应用 → 蓝牙 → 清除缓存） |

### 配对弹窗不出现

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 设备不要求配对 | 检查设备安全设置 | 设备端开启加密要求 |
| 已配对（Bonded） | getBondState() | 检查是否已在已绑定列表 |
| 系统 UI 被遮挡 | 检查是否有全屏 Activity | 确保配对弹窗能显示 |
| BroadcastReceiver 拦截 | 检查 ACTION_PAIRING_REQUEST | 确保未意外 abort |

### 多设备连接不稳定

| 可能原因 | 排查方法 | 解决方案 |
|---------|---------|---------|
| 超过芯片并发上限 | 统计当前连接数 | 控制并发连接数 ≤ 5 |
| Connection Interval 竞争 | HCI 日志分析 | 合理分配各设备的 Priority |
| 蓝牙带宽不足 | 观察吞吐下降 | 减少同时通信的设备数 |

## 不同芯片 / 厂商兼容性

### 常见芯片平台差异（高通 / 联发科 / 三星 Exynos）

| 维度 | 高通骁龙 | 联发科 | 三星 Exynos |
|------|---------|--------|-----------|
| BLE 5.0 支持 | 骁龙 845+ | 天玑 700+ | Exynos 990+ |
| 最大并发连接 | 10-15 | 7-10 | 7-10 |
| Extended Advertising | 骁龙 845+ | 天玑 1000+ | 有限 |
| LE Audio | 骁龙 8 Gen 1+ | 天玑 9000+ | Exynos 2200+ |
| 蓝牙栈稳定性 | 较好 | 一般 | 较好 |

### 厂商定制 ROM 的影响

| 厂商 | 已知问题 | 应对 |
|------|---------|------|
| 华为/荣耀 | 省电模式激进杀后台；部分机型蓝牙行为异常 | 引导用户加白名单 |
| 小米/Redmi | MIUI 蓝牙权限管理严格；后台限制强 | 引导开启自启动 |
| OPPO/OnePlus | ColorOS 后台限制；部分机型 MTU 协商异常 | MTU 做容错处理 |
| vivo | OriginOS 省电策略；扫描间隔可能被拉长 | 前台扫描 + 合理超时 |
| Samsung | 部分旧机型 status 133 频发 | 增加重试和延迟 |

### 兼容性测试建议

1. **覆盖主流芯片**：至少测试高通、联发科各 1-2 款
2. **覆盖主流 ROM**：华为、小米、OPPO、vivo、Samsung 各至少 1 款
3. **覆盖 Android 版本**：至少测试 Android 10/12/13+
4. **测试后台场景**：验证锁屏、后台运行、省电模式下的蓝牙行为
5. **压力测试**：长时间连接（>24 小时）、频繁断连重连、多设备并发

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

- [nRF Connect for Mobile](https://play.google.com/store/apps/details?id=no.nordicsemi.android.mcp)
- [Wireshark Bluetooth Analysis](https://www.wireshark.org/)
- [Android Bluetooth HCI Snoop Log](https://source.android.com/docs/core/connect/bluetooth/verifying_debugging)
- [Bluetooth GATT Error Codes](https://cs.android.com/android/platform/superproject/+/master:packages/modules/Bluetooth/system/stack/include/gatt_api.h)
- [Don't Kill My App — 各厂商后台限制](https://dontkillmyapp.com/)
- [Punch Through — Android BLE Guide](https://punchthrough.com/android-ble-guide/)

---
创建日期: 2026-04-06
最后更新: 2026-04-06
作者: <团队共建>
状态: 进行中
前置知识: 01-串口基础知识serial-fundamentals, 02-协议设计与数据解析protocol-design-and-parsing
关联模块: 04-原生串口方案native-serial-jni, 05-USB设备管理与权限usb-device-and-permissions
---

# USB 转串口方案（推荐）

## 方案概述

USB 转串口方案是 Android 串口开发的**首选方案**，适用于绝大多数场景。通过 USB OTG 接口连接 USB 转串口模块，借助 `usb-serial-for-android` 开源库实现免 root 串口通信。

**适用场景**：

- 普通 Android 手机/平板，无 root 权限
- 通过 USB OTG 连接 USB 转串口模块
- 大多数项目的首选方案

**整体架构**：

```mermaid
graph LR
    APP["Android 应用\nKotlin"] --> LIB["usb-serial-for-android\n串口驱动库"]
    LIB --> USB["Android USB Host API\nUsbManager"]
    USB --> CHIP["USB 转串口芯片\nCH340/CP2102"]
    CHIP --> MCU["MCU / 外设\nTTL 电平"]
```

## 添加依赖

```kotlin
// build.gradle.kts (app)
dependencies {
    implementation("com.github.mik3y:usb-serial-for-android:3.8.1")
}
```

```kotlin
// settings.gradle.kts
dependencyResolutionManagement {
    repositories {
        maven { url = uri("https://jitpack.io") }
    }
}
```

## 支持的 USB 芯片与兼容性

`usb-serial-for-android` 内置了主流 USB 转串口芯片的驱动：

| 芯片 | 厂商 | VendorId | ProductId | Driver 类 | 备注 |
|------|------|----------|-----------|-----------|------|
| CH340/CH341 | 南京沁恒 | 0x1A86 | 0x7523 | `Ch34xSerialDriver` | 国产芯片，价格低，兼容性好 |
| CP2102/CP2104 | Silicon Labs | 0x10C4 | 0xEA60 | `Cp21xxSerialDriver` | 稳定性好，驱动成熟 |
| FT232R/FT2232 | FTDI | 0x0403 | 0x6001 | `FtdiSerialDriver` | 老牌芯片，性能稳定，价格较高 |
| PL2303 | Prolific | 0x067B | 0x2303 | `ProlificSerialDriver` | 假芯片泛滥，注意采购渠道 |
| CDC/ACM | 通用 | — | — | `CdcAcmSerialDriver` | USB 标准协议，免驱 |

## 核心 API 详解

### UsbSerialProber

设备探测器，负责扫描已连接的 USB 设备并匹配合适的驱动：

```kotlin
// 使用内置的默认探测器
val prober = UsbSerialProber.getDefaultProber()
val drivers = prober.findAllDrivers(usbManager)

// 自定义探测器（支持非标设备）
val customTable = ProbeTable().apply {
    addProduct(0x1234, 0x5678, CdcAcmSerialDriver::class.java)
}
val customProber = UsbSerialProber(customTable)
```

### UsbSerialPort

核心接口，封装了串口的打开、配置和读写操作：

| 方法 | 说明 |
|------|------|
| `open(connection)` | 打开串口连接 |
| `close()` | 关闭串口连接 |
| `setParameters(baudRate, dataBits, stopBits, parity)` | 配置串口参数 |
| `read(buffer, timeout)` | 同步读取数据 |
| `write(data, timeout)` | 同步写入数据 |
| `isOpen` | 是否已打开 |
| `dtr` / `rts` | DTR/RTS 信号控制 |

### SerialInputOutputManager

库提供的异步读写管理器，内部维护一个独立线程进行持续读取：

```kotlin
val ioManager = SerialInputOutputManager(port, object : SerialInputOutputManager.Listener {
    override fun onNewData(data: ByteArray) {
        // 在后台线程中回调，收到数据
    }

    override fun onRunError(e: Exception) {
        // 连接异常
    }
})
ioManager.start()
```

## 完整使用流程

### 1. 设备发现

```kotlin
import android.hardware.usb.UsbManager
import com.hoho.android.usbserial.driver.UsbSerialProber

fun discoverDevices(usbManager: UsbManager): List<UsbSerialDriverInfo> {
    val availableDrivers = UsbSerialProber.getDefaultProber().findAllDrivers(usbManager)
    return availableDrivers.map { driver ->
        val device = driver.device
        UsbSerialDriverInfo(
            deviceName = device.deviceName,
            vendorId = device.vendorId,
            productId = device.productId,
            driverName = driver.javaClass.simpleName,
            portCount = driver.ports.size
        )
    }
}

data class UsbSerialDriverInfo(
    val deviceName: String,
    val vendorId: Int,
    val productId: Int,
    val driverName: String,
    val portCount: Int
)
```

### 2. 请求权限与打开连接

```kotlin
fun openSerialPort(
    usbManager: UsbManager,
    driverIndex: Int = 0,
    portIndex: Int = 0
): UsbSerialPort {
    val drivers = UsbSerialProber.getDefaultProber().findAllDrivers(usbManager)
    if (drivers.isEmpty()) throw IllegalStateException("未发现 USB 串口设备")

    val driver = drivers[driverIndex]
    val connection = usbManager.openDevice(driver.device)
        ?: throw SecurityException("无法打开 USB 设备，请检查权限是否已授予")

    return driver.ports[portIndex].apply {
        open(connection)
    }
}
```

### 3. 配置串口参数

```kotlin
port.setParameters(
    115200,                          // 波特率
    UsbSerialPort.DATABITS_8,        // 数据位
    UsbSerialPort.STOPBITS_1,        // 停止位
    UsbSerialPort.PARITY_NONE        // 校验位
)

// 部分芯片需要设置 DTR/RTS 信号才能正常通信
port.dtr = true
port.rts = true
```

### 4. 数据读写

**同步方式**（适合在协程中使用）：

```kotlin
// 发送
port.write(byteArrayOf(0x01, 0x03, 0x00, 0x00, 0x00, 0x0A), 1000)

// 接收
val buffer = ByteArray(4096)
val bytesRead = port.read(buffer, 1000)
if (bytesRead > 0) {
    val received = buffer.copyOf(bytesRead)
    // 处理数据
}
```

**异步方式**（使用 SerialInputOutputManager）：

```kotlin
val ioManager = SerialInputOutputManager(port, object : SerialInputOutputManager.Listener {
    override fun onNewData(data: ByteArray) {
        // 注意：此回调在后台线程，更新 UI 需切换到主线程
        runOnUiThread {
            handleReceivedData(data)
        }
    }

    override fun onRunError(e: Exception) {
        runOnUiThread {
            handleError(e)
        }
    }
})

// 可选：设置读取缓冲区大小
ioManager.readBufferSize = 4096

// 在线程池中启动
Executors.newSingleThreadExecutor().submit(ioManager)
```

**协程方式**（推荐，见 `06-通信架构设计`）：

```kotlin
fun CoroutineScope.startReading(
    port: UsbSerialPort,
    onData: (ByteArray) -> Unit
): Job = launch(Dispatchers.IO) {
    val buffer = ByteArray(4096)
    while (isActive && port.isOpen) {
        try {
            val bytesRead = port.read(buffer, 200)
            if (bytesRead > 0) {
                onData(buffer.copyOf(bytesRead))
            }
        } catch (e: IOException) {
            break
        }
    }
}
```

### 5. 关闭连接

```kotlin
// 按顺序关闭资源
ioManager.stop()         // 先停止异步读写
port.close()             // 再关闭串口
connection.close()       // 最后关闭 USB 连接
```

> **注意**：关闭顺序很重要。先停止 IO 管理器，再关闭端口，最后关闭 USB 连接。逆序操作可能导致异常。

## CDC/ACM 标准设备

USB CDC（Communication Device Class）是 USB 标准中定义的通信设备类，其中 ACM（Abstract Control Model）子类专门用于虚拟串口。

**优势**：

- 标准化协议，无需特定芯片驱动
- 越来越多的 MCU 原生支持 USB CDC（如 STM32 系列）
- 在 Android 上使用 `CdcAcmSerialDriver` 即可通信

```kotlin
// CDC 设备会被 getDefaultProber() 自动识别
// 如果未被自动识别，可手动添加到 ProbeTable
val table = ProbeTable().apply {
    addProduct(vendorId, productId, CdcAcmSerialDriver::class.java)
}
val prober = UsbSerialProber(table)
```

## 自定义 Prober 支持非标芯片

当使用非标准 USB 转串口芯片（如某些国产方案），默认 Prober 无法识别时：

```kotlin
/**
 * 创建支持自定义设备的探测器
 * 在默认探测器基础上追加自定义设备
 */
fun createCustomProber(): UsbSerialProber {
    val table = UsbSerialProber.getDefaultProbeTable().apply {
        // 添加自定义 CDC 设备
        addProduct(0x1234, 0x5678, CdcAcmSerialDriver::class.java)
        // 添加自定义 CH340 兼容设备
        addProduct(0xAAAA.toInt(), 0xBBBB.toInt(), Ch34xSerialDriver::class.java)
    }
    return UsbSerialProber(table)
}
```

## 与原生串口方案的对比

| 维度 | USB 转串口方案 | 原生串口方案（JNI） |
|------|---------------|-------------------|
| 是否需要 root | 否 | 是 |
| 适用设备 | 所有支持 USB OTG 的设备 | root 设备/定制板 |
| 硬件依赖 | USB 转串口模块 | 板载 UART |
| 延迟 | 较高（经过 USB 协议栈） | 较低（直接操作设备文件） |
| 稳定性 | 好（受 USB 接口质量影响） | 好（直连更可靠） |
| 芯片支持 | CH340/CP210x/FTDI/PL2303/CDC | 不涉及 |
| 社区维护 | **活跃**（usb-serial-for-android） | 维护较少 |
| **推荐度** | **✅ 首选** | 特定场景使用 |

## 踩坑记录

> 此区域供团队成员补充项目中遇到的真实案例。

| 日期 | 记录人 | 问题描述 | 解决方案 |
|------|--------|----------|----------|
| | | | |

## 参考资料

- [usb-serial-for-android - GitHub](https://github.com/mik3y/usb-serial-for-android)
- [Android USB Host API 官方文档](https://developer.android.com/develop/connectivity/usb/host)
- [USB CDC/ACM 规范](https://www.usb.org/document-library/class-definitions-communication-devices-12)
- [原生串口方案](04-原生串口方案native-serial-jni.md) — 本模块下一篇

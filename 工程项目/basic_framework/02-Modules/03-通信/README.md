# 通信模块总览

| 模块 | 传输介质 | 用途 | 状态 |
|---|---|---|---|
| Message Center | 内存复制 | 单板 Application 间发布订阅 | 已使用 |
| CAN Comm | CAN 分包 | 双板/多板结构体通信 | 已实现 |
| Remote Control | UART DMA | DJI DBUS、FS 遥控器解析 | 已使用 |
| Master Machine | UART 或 USB | 视觉上位机通信 | 已使用但有 TODO |
| HC05 | UART | 简单蓝牙收发 | 基础实现 |
| Unicomm | 未定 | 通用通信抽象 | 空文件，占位 |
| Standard CMD | 内存消息 | 统一多种控制源 | 空文件，占位 |

协议调试时分四层检查：物理连接 → BSP 是否收到 → 协议是否校验通过 → Application 是否消费数据。


# Modules 层总览

Module 把“硬件通信”变成“可被 Application 使用的功能”。例如 DJI 电机模块包含 CAN 实例和 PID，向上只暴露初始化、设定目标、启停等接口。

## 分类导航

- [算法](01-算法/README.md)
- [电机](02-电机/README.md)
- [通信](03-通信/README.md)
- [传感器](04-传感器/README.md)
- [系统设备](05-系统设备/README.md)

## 通用结构

典型 Module 包含：

1. `XXXInstance`：运行状态和所拥有的 BSP 实例；
2. `XXX_Init_Config_s`：硬件选择、参数、回调；
3. `XXXInit/Register()`：申请实例、注册 BSP、注册 Daemon；
4. `Decode/Callback()`：解析底层数据；
5. `Task/Control()`：周期运算；
6. `Set/Get/Send/Enable/Stop()`：Application 使用的接口。

## 内存模式

许多模块在初始化阶段通过 `malloc/zmalloc` 分配实例，之后不释放。这符合“设备在上电时创建、运行期永久存在”的嵌入式用法，但要关注：

- FreeRTOS heap 与 C 库 heap 是否一致；
- 分配失败是否检查；
- 初始化期总内存；
- 不要在高频任务里反复分配。


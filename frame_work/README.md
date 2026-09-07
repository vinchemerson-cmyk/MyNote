# basic_framework 学习笔记

这套笔记对应湖南大学 RoboMaster 跃鹿战队的 `basic_framework`。阅读时以项目当前代码为准，原项目文档作为补充。

## 推荐阅读顺序

1. [项目架构](00-项目总览/01-项目架构.md)
2. [启动流程](00-项目总览/02-启动流程.md)
3. [FreeRTOS 任务](00-项目总览/03-FreeRTOS任务.md)
4. [当前配置与主控制链](00-项目总览/04-当前配置与主控制链.md)
5. [Application 层](application.md)
6. [Modules 层](modules.md)
7. [BSP 层](bsp.md)
8. [构建与调试](04-构建与调试/README.md)

## 目录说明

- `00-项目总览`：先建立全局认识，理解启动、任务和数据流。
- `01-BSP`：片上外设封装，如 CAN、UART、SPI、GPIO。
- `02-Modules`：电机、传感器、算法、通信和系统服务。
- `03-Application`：机器人命令、底盘、云台和发射机构。
- `04-构建与调试`：CMake、工具链、常见错误和调试方法。

## 阅读原则

- 不要从 `Drivers` 或 `Middlewares` 开始逐行读，它们主要是第三方库。
- 每个模块先回答：输入是什么、输出是什么、由谁初始化、何时运行、依赖什么硬件。
- 优先沿一条完整链路阅读，例如“遥控器 → CMD → 底盘 → 电机 → CAN”。
- `Init/Register` 通常负责创建实例，`Task/Control` 是周期逻辑，`Callback/Decode` 是中断或接收路径，`Set/Get/Send` 是公开接口。

## 当前项目状态

- 当前使用 `ONE_BOARD` 单板模式。
- `RobotCMD` 和 `Chassis` 已启用。
- `Gimbal`、`Shoot` 的初始化和任务当前被注释。
- 部分应用代码仍是示例或待完成实现，不能把所有 TODO 当成可直接上场的功能。


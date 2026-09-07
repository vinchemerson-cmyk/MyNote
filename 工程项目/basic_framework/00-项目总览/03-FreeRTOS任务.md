# FreeRTOS 任务

项目使用 1 kHz 系统节拍，即 `osDelay(1)` 大约延迟 1 ms。任务均在 `application/robot_task.h` 中创建和实现。

| 任务 | 优先级 | 周期 | 主要内容 |
|---|---|---:|---|
| INS Task | AboveNormal | 1 ms | BMI088 读取、姿态解算、视觉数据发送 |
| Motor Task | Normal | 1 ms | 各类电机控制、PID、CAN 指令发送 |
| Daemon Task | Normal | 10 ms | 离线计数、错误回调、蜂鸣器任务 |
| Robot Task | Normal | 5 ms | CMD、底盘、云台、发射应用逻辑 |
| UI Task | Normal | 约 1 ms | 裁判系统 UI 更新和发送 |

## 任务分工

- Application 任务计算“目标是什么”。
- Motor 任务计算“怎样输出到电机”。
- 中断与回调负责及时接收数据，但应避免在中断内做耗时运算。
- Daemon 任务把“多久没收到数据”转换为离线状态和恢复动作。

## 时间测量

INS、Motor、Robot 和 Daemon 任务通过 `DWT_GetTimeline_ms()` 测量一次循环耗时；超出预期周期会写入 RTT 日志。

## 周期的真实含义

当前写法是“执行时间 + osDelay”，不是严格的绝对周期。若函数执行耗时 0.3 ms，再 `osDelay(1)`，实际周期会略大于 1 ms。需要更稳定周期时可以考虑 `vTaskDelayUntil()` 或硬件数据就绪中断。

## 代码组织问题

任务句柄和函数实现都写在 `robot_task.h` 中。当前只有 `robot.c` 包含它，所以能够工作；更规范的做法是把定义移到 `robot_task.c`，头文件只保留 `extern` 变量和函数声明。


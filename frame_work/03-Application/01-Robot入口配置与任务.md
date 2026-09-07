# Robot 入口、配置与任务

## robot.c

提供两个最高层接口：

```c
void RobotInit(void);
void RobotTask(void);
```

条件编译决定当前板运行哪些应用：

- `ONE_BOARD`：CMD、Gimbal、Shoot、Chassis；
- `GIMBAL_BOARD`：CMD、Gimbal、Shoot；
- `CHASSIS_BOARD`：Chassis。

不过当前代码手动注释了 Gimbal/Shoot，所以宏允许并不代表实际启用。

## robot_def.h

包含三类内容：

1. 板型和通信选择宏；
2. 机器人机械与方向参数；
3. Application 之间传输的模式枚举、命令和反馈结构体。

关键机械参数：

- 云台与底盘对齐编码器值；
- Pitch 水平零位和软限位；
- 每发拨盘角度、减速比和一圈弹量；
- 轴距、轮距、轮半径；
- IMU 到云台各轴方向。

换机器人时不能只改 PID，必须先核对这些宏。

## 单板与双板

单板使用 Message Center 传递底盘命令和反馈。双板模式下，云台板 CMD 与底盘板 Chassis 使用 CANComm：

```text
Gimbal board: tx 0x312 / rx 0x311
Chassis board: tx 0x311 / rx 0x312
```

传输结构体使用 `#pragma pack(1)`，两块板必须使用同一份类型定义。

## robot_task.h

创建 INS、Motor、Daemon、Robot、UI 五个任务。当前把实现写在头文件里，最好后续重构到 `.c` 文件。


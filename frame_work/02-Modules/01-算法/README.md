# 算法模块

| 文件 | 功能 |
|---|---|
| `controller.c/h` | PID 与改进项 |
| `kalman_filter.c/h` | 通用矩阵卡尔曼滤波器 |
| `QuaternionEKF.c/h` | 面向 IMU 姿态解算的四元数 EKF |
| `crc8.c/h`、`crc16.c/h` | 通信校验 |
| `user_lib.c/h` | 限幅、角度、向量、平均滤波和内存工具 |

阅读建议：先掌握 PID，再读 INS 如何调用四元数 EKF；通用卡尔曼只有在具体应用需要状态估计时再深入。


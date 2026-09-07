# Gimbal

Gimbal 控制两个 GM6020：Yaw 位于 CAN1 ID1，Pitch 位于 CAN2 ID2。

## 初始化

先调用 `INS_Init()` 获取姿态指针，再建立两套角度-速度串级 PID：

- Yaw 角度反馈：`YawTotalAngle`；
- Yaw 速度反馈：`Gyro[2]`；
- Pitch 角度反馈：`Pitch`；
- Pitch 速度反馈：`Gyro[0]`。

然后注册 `gimbal_cmd` 订阅者和 `gimbal_feed` 发布者。

## 周期模式

- `GIMBAL_ZERO_FORCE`：停止两个电机；
- `GIMBAL_GYRO_MODE`：使用 INS 外部反馈闭环；
- `GIMBAL_FREE_MODE`：注释称编码器自由模式，但当前代码仍然把角度和速度反馈切到 `OTHER_FEED`，与注释不一致。

任务最后发布完整 IMU 姿态和 Yaw 电机单圈角度。CMD 用单圈角度计算云台与底盘的偏角。

## 当前状态与风险

- `RobotInit/RobotTask` 中入口被注释；
- Pitch/Yaw PID 只是示例参数；
- Pitch 重力补偿未实现；
- Pitch 软件限位未实现；
- 模式切换没有参考值对齐，重新启用时可能跳变；
- `GIMBAL_FREE_MODE` 的实现与设计说明需要统一；
- 启用前必须架空测试电机方向和 IMU 轴映射。


# 卡尔曼滤波与四元数 EKF

## 通用 KalmanFilter

`kalman_filter` 使用 CMSIS-DSP 矩阵函数实现可配置维度的离散卡尔曼滤波。

主要状态：

- `xhat`：状态估计；
- `P`：估计协方差；
- `F/B/H`：状态转移、控制和观测矩阵；
- `Q/R`：过程噪声与测量噪声；
- `K`：卡尔曼增益。

调用流程由 `Kalman_Filter_Update()` 串起预测和校正，也可以分别调用各步骤以插入自定义模型。

使用前必须明确状态向量、输入向量、观测向量和单位。矩阵维度错误比参数不好更常见。

## QuaternionEKF

`QuaternionEKF` 是针对 IMU 姿态的扩展卡尔曼滤波器。输入陀螺仪角速度、加速度和 `dt`，内部维护四元数与相关估计量。

```c
IMU_QuaternionEKF_Init(...);
IMU_QuaternionEKF_Update(gx, gy, gz, ax, ay, az, dt);
```

INS 任务读取 BMI088 后调用它，再输出 Yaw/Pitch/Roll。四元数避免欧拉角在特定姿态下的奇异问题。

## 需要关注

- 陀螺仪单位是 rad/s 还是 deg/s；
- 坐标系方向和轴映射；
- 加速度需归一化并受运动加速度干扰；
- `dt` 必须来自真实采样周期；
- 上电初始四元数决定初始姿态；
- 噪声参数影响响应速度与抗抖动能力。


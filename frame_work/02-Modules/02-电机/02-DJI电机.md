# DJI 电机

支持 RoboMaster 常用的 GM6020、M3508/C620、M2006/C610。

## 初始化

```c
DJIMotorInstance *DJIMotorInit(Motor_Init_Config_s *config);
```

初始化会：

1. 创建电机实例；
2. 初始化角度、速度、电流 PID；
3. 根据型号、CAN 总线和电机 ID 分配发送组；
4. 计算反馈 ID 并注册 CAN 接收；
5. 注册 Daemon，反馈超时后报告离线；
6. 默认使能电机。

## 反馈解析

`DecodeDJIMotor()` 从 8 字节反馈中解析：

- 编码器值和单圈角度；
- 转速；
- 实际电流；
- 温度；
- 跨零点累计圈数与多圈总角度。

多圈算法假设相邻两次采样电机转角小于 180°。反馈丢失过久会导致跨圈判断不可靠。

## 四电机分组发送

DJI 电调协议允许四个电机共用一帧，每个电机占两个字节。模块维护 CAN1/CAN2 上的 `0x1FF、0x200、0x2FF` 六个发送组，控制任务集中填充并发送。

## 主要接口

```c
DJIMotorSetRef(motor, ref);
DJIMotorOuterLoop(motor, loop);
DJIMotorChangeFeed(motor, loop, source);
DJIMotorEnable(motor);
DJIMotorStop(motor);
DJIMotorControl();
```

`Stop` 会让最终 CAN 字段清零；`SetRef` 只更新目标，真正计算和发送发生在周期任务。

## 调试顺序

先看 CAN 是否在线和反馈是否正确，再确认方向、反馈源和单位，最后才调 PID。编码器闭环与 IMU 闭环切换时要避免参考值突变。


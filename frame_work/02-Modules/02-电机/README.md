# 电机模块总览

电机层负责把 Application 给出的角度、速度或力矩目标转换成具体电机协议报文。

## 文件

- [公共抽象](01-电机公共抽象.md)
- [DJI 电机](02-DJI电机.md)
- [DM 电机](03-DM电机.md)
- [HT04 与 LK9025](04-HT04与LK9025.md)
- [舵机与步进电机](05-舵机与步进电机.md)
- [电机任务与功率控制](06-电机任务与功率控制.md)

## 运行关系

```text
Application 调用 SetRef
→ 实例保存 pid_ref
→ 1 kHz MotorControlTask
→ 对每类已注册电机执行控制
→ PID/协议编码
→ BSP CAN/PWM 发送
```

## 当前默认底盘

四个 M3508 不是通过普通 `DJIMotorInit()` 注册，而是通过 `PowerControlInit()` 注册到功率控制模块。因此 `PowerControl()` 才是默认底盘电机的实际闭环和发送路径。


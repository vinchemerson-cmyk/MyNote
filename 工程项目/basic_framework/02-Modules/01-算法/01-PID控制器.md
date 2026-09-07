# PID 控制器

## 主要接口

```c
void PIDInit(PIDInstance *pid, PID_Init_Config_s *config);
float PIDCalculate(PIDInstance *pid, float measure, float ref);
void PIDClear(PIDInstance *pid);
```

`PIDCalculate()` 输入测量值和参考值，输出控制量。电机模块把它用于角度环、速度环和电流环的串级控制。

## PIDInstance

实例保存：

- `Kp/Ki/Kd`；
- 当前和历史误差；
- P/I/D 分量与最终输出；
- 输出、积分、死区限制；
- 微分/输出低通参数；
- 改进功能标志；
- 误差类型与错误处理信息。

## 可选改进

`PID_Improvement_e` 是位标志，可以组合启用：

- 梯形积分；
- 变速积分；
- 积分限幅；
- 微分先行/微分对测量；
- 输出滤波；
- 不完全微分等。

阅读时重点确认每个标志在哪个计算阶段生效，以及所用单位是否匹配。

## 串级 PID

电机常用流程：

```text
角度误差 → 角度 PID → 目标速度
目标速度 - 实际速度 → 速度 PID → 目标电流/力矩
```

不是所有电机都启用所有环。`close_loop_type` 表示启用哪些环，`outer_loop_type` 表示当前最外层参考量是什么。

## 调参原则

- 先内环后外环；
- 先 P，再按需要添加 I/D；
- 先确认方向、单位、反馈源，再调参数；
- 输出饱和时继续增大积分会导致 windup，应启用积分限制；
- 使用 DWT/示波工具观察 `ref、measure、output`，不要只凭电机声音判断。


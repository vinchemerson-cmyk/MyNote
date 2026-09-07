# TFminiPlus 测距

这是北醒 TFmini Plus 单点激光雷达的简单 I²C 阻塞读取实现。

## 接口

```c
TFMiniInstance *TFMiniRegister(I2C_HandleTypeDef *i2c);
float GetDistance(TFMiniInstance *instance);
```

注册时使用设备地址 `0x10`，随后 DWT 等待 0.5 秒。读取时发送 5 字节命令，再接收响应并解析距离、信号强度和模式。

## 当前问题

- 使用阻塞 I²C，调用耗时较大；
- `TFMiniInstance.buf` 当前定义为 9 字节，但 `GetDistance()` 请求接收 11 字节，存在缓冲区越界风险，必须先修复；
- 没有检查响应帧头、长度、校验和或 I²C 返回状态；
- 返回类型是 `float`，但内部距离是 `uint16_t`，单位需要结合模块配置确认；
- 全局只保存一个 `tfmini` 指针，实际按单实例使用。

这个模块目前不适合未经修改直接用于安全相关控制。


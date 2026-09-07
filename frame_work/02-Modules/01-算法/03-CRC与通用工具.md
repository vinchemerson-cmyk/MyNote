# CRC 与通用工具

## CRC8/CRC16

`crc8` 和 `crc16` 为通信协议提供校验。CAN 多板通信和上位机协议会使用这些函数。

接口示例：

```c
uint8_t crc_8(const uint8_t *data, uint16_t len);
uint16_t crc_16(const uint8_t *data, uint16_t len);
uint16_t crc_modbus(const uint8_t *data, uint16_t len);
```

CRC 算法必须与对端保持完全一致：多项式、初值、输入输出反转和最终异或任何一个不同都会校验失败。裁判系统另有协议专用 `crc_ref`，不要混用。

## user_lib

常用函数分为几类：

- 内存：`zmalloc()`；
- 限幅：`abs_limit`、`float_constrain`、`int16_constrain`；
- 周期角：`loop_float_constrain`、`theta_format`；
- 数学：`Sqrt`、`sign`、`float_rounding`；
- 三维向量：归一化、模长、点乘、叉乘；
- 简单滤波：`AverageFilter`；
- 矩阵包装：`MatInit` 等。

## general_def.h

这是跨模块的基础宏定义，通常包含 PI、角度弧度转换、编码器系数、大小值等。出现单位不明时应同时检查 `general_def.h` 和调用模块的注释。


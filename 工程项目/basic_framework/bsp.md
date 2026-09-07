# BSP 层索引

BSP（Board Support Package）负责把 STM32 HAL 封装为实例化接口。Module 层只需要保存 `XXXInstance *`，不必重复编写 HAL 回调分发代码。

## 文件

- [BSP 总览](01-BSP/README.md)
- [CAN](01-BSP/01-CAN.md)
- [USART 与 USB](01-BSP/02-USART与USB.md)
- [SPI 与 IIC](01-BSP/03-SPI与IIC.md)
- [GPIO、PWM 与 ADC](01-BSP/04-GPIO-PWM-ADC.md)
- [DWT、日志与 Flash](01-BSP/05-DWT-日志-Flash.md)
- [BSP 初始化、回调任务和使用模式](01-BSP/06-初始化与回调模式.md)

## 通用模式

```text
Module 准备 XXX_Init_Config_s
→ XXXRegister()
→ BSP 保存实例与回调
→ HAL 中断发生
→ BSP 根据硬件句柄查找实例
→ 调用 Module 注册的 callback
```

# BSP SPI 与 IIC

## SPI

SPI BSP 面向片选独立的外部器件。`SPIInstance` 保存 SPI 句柄、CS 端口/引脚、工作模式、回调和父模块指针。

### 接口

```c
SPIInstance *SPIRegister(SPI_Init_Config_s *config);
void SPITransmit(SPIInstance *instance, uint8_t *data, uint8_t len);
void SPIRecv(SPIInstance *instance, uint8_t *data, uint8_t len);
void SPITransRecv(SPIInstance *instance, uint8_t *rx, uint8_t *tx, uint8_t len);
void SPISetMode(SPIInstance *instance, SPI_TXRX_MODE_e mode);
```

支持阻塞、中断和 DMA 模式。收发前拉低 CS，完成后恢复 CS；异步模式依靠 HAL SPI 完成回调查找实例并调用 Module 回调。

### 主要使用者

- 旧版 BMI088 驱动直接使用 HAL/SPI 中间层；
- 新版 `modules/BMI088` 使用 `SPIInstance`；
- 不同器件共用一个 SPI 总线时，必须保证片选和异步传输状态正确。

## IIC

IIC BSP 保存 I²C 句柄、设备地址、工作模式和接收回调，支持：

- 主机发送、接收；
- Sequential Frame 模式；
- 8/16 位寄存器地址的 Memory Read/Write；
- 阻塞、中断、DMA 工作模式。

### 接口

```c
IICInstance *IICRegister(IIC_Init_Config_s *config);
void IICSetMode(IICInstance *instance, IIC_Work_Mode_e mode);
void IICTransmit(...);
void IICReceive(...);
void IICAccessMem(...);
```

### 注意

- STM32 HAL 中设备地址经常要求左移一位，注册时必须确认模块代码采用的是 7 位地址还是 HAL 格式。
- 当前回调按 I²C 句柄和最近一次设备访问状态进行分发，并发访问同一总线时需额外仲裁。


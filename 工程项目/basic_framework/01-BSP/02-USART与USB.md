# BSP USART 与 USB

## USART

USART BSP 的核心是 `HAL_UARTEx_ReceiveToIdle_DMA()`：DMA 接收缓冲区未满但串口出现 IDLE 时，也能触发回调，适合 DBUS、视觉和裁判系统等帧协议。

### 核心类型

`USARTInstance` 保存 UART 句柄、接收缓冲区、缓冲区长度和 Module 回调。`USART_Init_Config_s` 用于注册。

### 接口

```c
USARTInstance *USARTRegister(USART_Init_Config_s *config);
void USARTServiceInit(USARTInstance *instance);
void USARTSend(USARTInstance *instance, uint8_t *buf, uint16_t len,
               USART_TRANSFER_MODE mode);
uint8_t USARTIsReady(USARTInstance *instance);
```

发送支持阻塞、中断和 DMA 三种模式。

### 接收链

```text
UART DMA/IDLE
→ HAL_UARTEx_RxEventCallback
→ 按 UART 句柄查找实例
→ module_callback()
→ 清理缓冲区
→ 重新启动 ReceiveToIdle DMA
```

注册和每次重启接收后都会关闭 DMA Half Transfer 中断，避免半传输和 IDLE 对同一帧产生重复处理。

### 注意

`USARTIsReady()` 当前实现使用了位或 `|` 判断状态，逻辑值得复核；通常状态位判断应使用按位与或直接比较。

## USB

USB BSP 当前只封装 CDC/VCP：

```c
uint8_t *USBInit(USB_Init_Config_s config);
void USBTransmit(uint8_t *buffer, uint16_t len);
```

初始化配置包含收发回调。视觉模块可通过 `VISION_USE_VCP` 选择 USB 发送。

### 注意

此实现修改了 CubeMX 生成的 `usbd_cdc_if.c` 接收函数，重新生成 CubeMX 工程时可能被覆盖，应检查 USER CODE 区或保存自定义模板。


# BSP CAN

## 职责

`bsp/can` 为 CAN1/CAN2 上的设备维护实例，自动配置过滤器，并在接收中断中按照“总线 + 标准 ID”找到对应模块。

## 核心类型

`CANInstance` 保存：

- `can_handle`：CAN1 或 CAN2；
- `tx_id/rx_id`：发送和接收标准 ID；
- `tx_buff/rx_buff`：8 字节缓冲区；
- `txconf`：HAL 发送头；
- `can_module_callback`：收到本设备报文后的解析函数；
- `id`：指向所属 Module 实例的父指针。

`CAN_Init_Config_s` 是注册时使用的精简配置。

## 公开接口

```c
CANInstance *CANRegister(CAN_Init_Config_s *config);
void CANSetDLC(CANInstance *instance, uint8_t length);
uint8_t CANTransmit(CANInstance *instance, float timeout);
```

## 注册过程

第一次注册会启动 CAN1/CAN2 并打开 FIFO0/FIFO1 消息中断。每次注册都会：

1. 检查实例数量上限；
2. 分配并清零 `CANInstance`；
3. 复制句柄、ID、回调和父指针；
4. 为 `rx_id` 配置 BxCAN 过滤器；
5. 保存到全局实例数组。

## 接收过程

```text
CAN FIFO 中断
→ CANFIFOxCallback
→ HAL_CAN_GetRxMessage
→ 遍历实例数组
→ 匹配 can_handle 与 rx_id
→ 复制到实例 rx_buff
→ 调用 can_module_callback(instance)
```

## 发送过程

模块先写入实例的 `tx_buff`，再调用 `CANTransmit()`。函数会等待发送邮箱空闲，但有超时保护；超时时间不应超过调用任务的周期。

## 注意点

- 单帧最大 8 字节；长数据由 `can_comm` 分包。
- DJI 电机为了四电机共用一帧，自己维护发送用 `CANInstance`，接收实例仍通过 BSP 注册。
- 注册 ID 冲突、总线负载和发送超时是调试重点。


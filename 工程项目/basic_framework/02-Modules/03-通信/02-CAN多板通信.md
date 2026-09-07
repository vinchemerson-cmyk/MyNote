# CAN Comm 多板通信

## 用途

CAN 单帧最多 8 字节，`can_comm` 用分包协议传输最长 60 字节的固定长度结构体，主要用于云台板与底盘板之间交换命令和反馈。

## 帧格式

```text
's' | data_len | payload | CRC8 | 'e'
```

总长度是有效数据长度加 4。发送时按 8 字节切分为多帧，最后一帧会修改 DLC。

## 接收状态机

1. 等待首字节 `'s'`；
2. 检查报文中声明的长度是否等于初始化配置；
3. 按收到顺序拼接每个 CAN 帧；
4. 达到预期总长度后检查尾字节和 CRC8；
5. 校验成功则复制到 `unpacked_recv_data`；
6. 设置更新标志并喂 Daemon；
7. 重置接收状态。

## 接口

```c
CANCommInstance *CANCommInit(CANComm_Init_Config_s *config);
void CANCommSend(CANCommInstance *instance, uint8_t *data);
void *CANCommGet(CANCommInstance *instance);
uint8_t CANCommIsOnline(CANCommInstance *instance);
```

`CANCommGet()` 返回内部缓冲区，需要转换为约定类型；传输结构体应使用 `#pragma pack(1)`，两端编译配置和类型定义必须一致。

## 限制

- 不支持动态包长；
- 没有帧序号，丢帧/插帧时只能靠头尾和长度重置；
- 同一个 CAN ID 的多包发送需要保持顺序；
- 发送多帧会增加总线负载；
- 初始化时应明确 `daemon_count`，零值会由 Daemon 使用默认值。


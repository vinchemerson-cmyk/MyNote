# Unicomm 与标准命令

## Unicomm

`modules/unicomm/unicomm.c` 和 `.h` 当前为空。文档表达的方向是建立统一通信抽象，让不同物理链路共享更一致的收发接口，但尚未实现。

可能需要统一的内容包括：

- UART/CAN/USB 传输后端；
- 固定/动态长度协议；
- 编解码和校验；
- 超时与在线状态；
- 发送队列和并发保护。

## Standard CMD

`modules/standard_cmd/std_cmd.c` 和 `.h` 当前也为空。目标是把遥控器、键鼠、视觉、图传链路、手柄等不同控制源统一成标准命令，再交给 RobotCMD。

理想关系：

```text
Remote / Keyboard / Vision / Gamepad
→ 各自解析原始数据
→ Standard CMD 统一语义和量纲
→ RobotCMD 做模式仲裁与安全处理
→ Application 控制消息
```

当前这些转换逻辑直接写在 `robot_cmd.c` 的 `RemoteControlSet()` 和 `MouseKeySet()` 中，所以 Standard CMD 仍是架构规划，而不是实际运行模块。


# Daemon 守护模块

Daemon 是软件离线看门狗：设备每次收到有效数据就“喂狗”，周期任务递减计数，归零后调用离线回调。

## 接口

```c
DaemonInstance *DaemonRegister(Daemon_Init_Config_s *config);
void DaemonReload(DaemonInstance *instance);
uint8_t DaemonIsOnline(DaemonInstance *instance);
void DaemonTask(void);
```

## 时间换算

Daemon Task 每 10 ms 执行一次，所以：

```text
超时时间 ≈ reload_count × 10 ms
```

例如遥控器 `reload_count = 10`，约 100 ms 无数据判定离线；裁判系统 30 对应约 300 ms。

## owner_id

一个模块可能有多个实例，`owner_id` 指向所属设备。离线时统一调用：

```c
callback(owner_id);
```

回调再转换回具体类型，以便停止某个电机或重启某一路 UART。

## 当前问题

- 实例数组没有显式数量越界检查；
- 初始化先设置 `init_count`，随后又被 `reload_count` 覆盖，`init_count` 实际没有按设计生效；
- 计数归零后，每次 Daemon Task 都会重复调用离线回调，直到重新喂狗；
- Daemon 只报告/恢复模块，整车是否急停仍要由 RobotCMD 安全逻辑决定。


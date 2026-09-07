# Application 层索引

Application 层描述“机器人要做什么”，不直接处理 CAN/UART 中断。不同应用通过 Message Center 发布和订阅结构体消息，尽量避免相互包含。

## 文件

- [Application 总览](03-Application/README.md)
- [Robot 入口、配置与任务](03-Application/01-Robot入口配置与任务.md)
- [RobotCMD](03-Application/02-RobotCMD.md)
- [Chassis](03-Application/03-Chassis.md)
- [Gimbal](03-Application/04-Gimbal.md)
- [Shoot](03-Application/05-Shoot.md)

## 核心关系

```text
RobotCMD 发布 chassis_cmd / gimbal_cmd / shoot_cmd
Chassis 订阅 chassis_cmd，发布 chassis_feed
Gimbal 订阅 gimbal_cmd，发布 gimbal_feed
Shoot 订阅 shoot_cmd，发布 shoot_feed
```

控制与反馈结构体统一定义在 `application/robot_def.h`，这是理解 Application 层最重要的文件。

# OLED

OLED 模块面向常见 128×64 I²C 屏幕，维护 `OLED_GRAM[128][8]` 显存并提供绘图和文字接口。

## 功能

- 初始化、开关显示；
- 清空、全亮、反色显存；
- 设置位置；
- 点、线；
- 字符串与格式化输出；
- 刷新整屏；
- Logo。

## 接口示例

```c
OLED_init();
OLED_operate_gram(PEN_CLEAR);
OLED_draw_line(...);
OLED_printf(row, col, "value=%d", value);
OLED_refresh_gram();
```

## 实现特点

代码直接使用全局 `hi2c2` 和 `HAL_I2C_Master_Transmit()`，没有采用 BSP IIC 实例，因此硬件耦合较强。每个命令/数据字节都是阻塞发送，整屏刷新会产生大量 I²C 事务。

## 使用建议

- 不要在高优先级或 1 kHz 控制任务中刷新整屏；
- 降低 UI 刷新率，只更新变化区域；
- 与同一 I²C 总线上的传感器并发使用时需要互斥；
- 若迁移板卡，应把 `hi2c2` 和地址放入配置并复用 BSP IIC。


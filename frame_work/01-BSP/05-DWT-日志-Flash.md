# BSP DWT、日志与 Flash

## DWT

DWT 使用 Cortex-M4 的 CYCCNT 周期计数器提供高精度时间。

常用接口：

```c
DWT_Init(168);
DWT_GetDeltaT(&last_count);
DWT_GetTimeline_ms();
DWT_GetTimeline_us();
DWT_Delay(delay_s);
```

用途：控制器 `dt`、任务耗时监控、通信超时、非 SysTick 延时。计数器溢出由实现进行时间累积处理。

`DWT_Delay()` 是忙等待，会占用 CPU；初始化阶段可用，FreeRTOS 任务中长时间等待应使用 `osDelay()`。

## 日志

日志基于 SEGGER RTT，不占用业务串口。`BSPLogInit()` 初始化 RTT，宏通常区分 INFO、WARNING、ERROR。

优势：速度快、对实时性影响相对较小、适合连接 J-Link/DAP 调试器查看。

注意：高频任务中大量打印仍可能阻塞或显著扰动时序；打印浮点数也会增加代码体积和执行开销。

## Flash

Flash BSP 提供扇区地址、擦除、单/多区域写入和读取接口。

```c
flash_erase_address(...);
flash_write_single_address(...);
flash_write_muli_address(...);
flash_read(...);
get_next_flash_address(...);
```

适合保存校准参数和掉电配置。

### 风险

- 擦除单位是扇区，不是单字节；
- 写入前通常必须擦除；
- 写擦期间会影响从 Flash 取指和实时任务；
- 地址必须避开程序代码与链接脚本已使用区域；
- Flash 有写擦寿命，不适合高频记录。


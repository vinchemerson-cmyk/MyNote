# BSP GPIO、PWM 与 ADC

## GPIO

`GPIOInstance` 封装端口、引脚、EXTI 模式、回调和父模块指针。

接口包括：

```c
GPIOInstance *GPIORegister(GPIO_Init_Config_s *config);
void GPIOToggel(GPIOInstance *instance);
void GPIOSet(GPIOInstance *instance);
void GPIOReset(GPIOInstance *instance);
GPIO_PinState GPIORead(GPIOInstance *instance);
```

若配置 EXTI，`HAL_GPIO_EXTI_Callback()` 会按引脚遍历实例并调用回调。这里只按 Pin 匹配；不同 GPIO 端口使用相同 Pin 编号时要确认是否会发生歧义。

主要使用场景：BMI088 数据就绪中断、普通控制引脚和状态输入。

## PWM

`PWMInstance` 保存定时器、通道、周期、占空比和完成回调。

接口包括：

```c
PWMInstance *PWMRegister(PWM_Init_Config_s *config);
void PWMStart(PWMInstance *pwm);
void PWMStop(PWMInstance *pwm);
void PWMSetDutyRatio(PWMInstance *pwm, float ratio);
void PWMSetPeriod(PWMInstance *pwm, float period);
void PWMStartDMA(PWMInstance *pwm, uint32_t *data, uint32_t size);
```

主要使用场景：蜂鸣器、舵机、BMI088 加热控制，以及可能的灯带 DMA 波形。

## ADC

`bsp/adc` 当前 `.c/.h` 基本为空，是预留模块。项目虽然通过 CubeMX 初始化 ADC1，但尚未形成与其他 BSP 一致的 `ADCInstance + ADCRegister` 接口。

如果后续完善，应考虑：

- 单通道轮询与多通道扫描；
- DMA 循环采样；
- 原始值到电压/温度的标定；
- 回调和数据所有权；
- ADC 采样时间与源阻抗。


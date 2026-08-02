# 20251211_FOC

基于 STM32F103C8T6 的无刷电机 FOC 矢量控制工程，STM32CubeMX 生成外设代码，Keil MDK-ARM 编译。

## 功能
- TIM2 中心对齐 PWM，10 kHz 载波（ARR = 3600）
- TIM2_CC2 触发 ADC1 双通道电流采样（PA4/PA5），DMA 搬运
- AS5600 磁编码器（I2C1，400 kHz，DMA 连续读取）测量角度
- 速度环 + d/q 电流环 PID，SVPWM 输出
- 过流检测（PA6 关断驱动），PA7 驱动使能，USART1 @115200 调试输出

## 目录结构
```
Src/        CubeMX 生成的初始化代码（main、外设）
Inc/        外设头文件
MDK-ARM/    Keil 工程 + FOC 算法模块（foc_*.c、pid.c、foc_math.c 等）
Drivers/    STM32F1 HAL / CMSIS 库
20251211_FOC.ioc  CubeMX 工程配置
```

## 构建
Keil 打开 `MDK-ARM/20251211_FOC.uvprojx` 编译下载，或命令行：

```powershell
& 'D:\App\Keil\Keil_v5\UV4\UV4.exe' -b '.\MDK-ARM\20251211_FOC.uvprojx'
```

## 控制架构（当前实现）
- 电流采样：TIM2 下降计数匹配 CC2 时触发 ADC1，顺序采样两个通道，DMA 完成中断做过流判断
- 控制环：TIM3 1 kHz 中断执行角度/转速/速度环/电流环/SVPWM 并更新占空比
- 注意：当前电流环与速度环同为 1 kHz，PWM 为 10 kHz；后续建议将电流环迁入 ADC DMA 完成回调（10 kHz）

## 硬件接口
| 外设 | 引脚 |
|---|---|
| 三相 PWM | TIM2 CH1/CH3/CH4 → PA0/PA2/PA3 |
| ADC 触发源 | TIM2 CH2 → PA1 |
| 电流采样 | ADC1_IN4/IN5 → PA4/PA5 |
| 过流关断 | PA6 |
| 驱动使能 | PA7 |
| 编码器 | I2C1 → PB6(SCL)/PB7(SDA) |
| 状态灯 | PC13 |
| 调试串口 | USART1 → PA9(TX)/PA10(RX) @115200 |

# capability 能力

STM32 can do what 51 can do and can't do

# STM 32 series

![](../_resources/2024-06-06-20-01-07-image.png)

![](../_resources/2024-06-06-20-01-16-image.png)

# naming rules

![](../_resources/2024-06-06-20-02-15-image.png)

# pins引脚

![](../_resources/2024-06-06-20-25-01-image.png)

![](../_resources/2024-06-06-20-25-16-image.png)

# Minimum System最小系统

The minimum requirements for ensuring the normal operation of the MCU, which generally refers to the power supply, reset, oscillator, BOOT

> 保证MCU运行的最低要求，，一般是指 MCU 的供电、复位、晶振、
> BOOT 等部分

![](../_resources/2024-06-06-20-28-07-image.png)

![](../_resources/2024-06-06-20-28-44-image.png)

The miminum system is completeed after designing the above pins, what to do next is allocating IO

# IO allocation

allocate the IO of the specific peripherals first, then allocate the universal IO, lastly, fine-tune IO 微调IO

![](../_resources/2024-06-07-20-44-23-image.png)

> pull-up resistor上拉电阻： 
> 
> pull-down resistor 下拉电阻

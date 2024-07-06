# what's Hal library

## API aplication program interface

familiar to function

## handle

be equivalent to the pointer to a resource(function, mem and so on)

## HAL(hardware abstruction layer)

- HAL library is a set of encapsulated封装好的 drivers
- can be used in different devices

## STD(Standard Peripheral Libraries)标准外设库

- be familiar to directly control registers
- can't be used in STMCUBEIDE
- STD of different devices are different

## LL(Low Layer)

- be familiar to directly control resgisters
- can be used in STMCUBEIDE
- can be used independently and can also used with HAL to optimize优化 the program

# file instroduction of HAL

| 文件                                                                                                     | 描述                                                                                                                                                        |
| ------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sm32f1xx_hal.c <br>stm32f1xx_hal.h                                                                     | 初始化 HAL 库，（比如 HAL_Init，HAL_DeInit，HAL_Delay 等），主要实现 HLA 库的初始化、系 统滴答，HAL 库延时函数、IO 重映射和 DBGMCU 功能。HAL 的头文件应被用户代码所包 含。                                       |
| stm32f1xx_hal_conf.h <br>HAL 库中本身没有这个文件，可以自行定义，也可以直接使用“Inc”文件夹下 stm32f1xx_hal_conf_template.h的内容作为参考模版 | HAL 用户配置文件，stm32f1xx_hal.h 引用了这个文件，用来对 HAL 库进行裁剪。由于 Hal 库的很多配置都是通过预编译的条件宏来决定是否使用这一 HAL 库的功能，这也是当前的主流库如LWIP/FreeRTOS 等的做法，无需修改库函数的源码，通过使能/不使能一些宏来实现库函数的裁剪。 |
| stm32f1xx_hal_def.h                                                                                    | 通用 HAL 库资源定义，包含 HAL 的通用数据类型定义，声明、枚举，结构体和宏定义。如 HAL函数操作结果返回值类型 HAL_StatusTypeDef 就是在这个文件中定义的。                                                               |
| stm32f1xx_hal_cortex.h <br>stm32f1xx_hal_cortex.c                                                      | 它是一些 Cortex 内核通用函数声明和定义，例如中断优先级 NVIC 配置，MPU，系统软复位以及Systick 配置等，与前面 core_cm3.h 的功能类似。                                                                      |
| stm32f1xx_hal_ppp.c <br>stm32f1xx_hal_ppp.h                                                            | 外设驱动函数。对于所有 STM32 的驱动名称都相同，ppp 代表一类外设，包含该外设的操作 API 函数 。 当 ppp 为 adc 时 ， 这 个 函 数 就 是stm32f1xx_hal_adc.c/h，可以分别在 Src/Inc 目录下找到。                             |
| stm32f1xx_hal_ppp_ex.c <br>stm32f1xx_hal_ppp_ex.h                                                      | 外设特殊功能的 API 文件，作为标准外设驱动的功能补充和扩展。针对部分型号才有的特殊外设作功能扩展，或外设的实现功能与标准方式完全不同的情况下作重新初始化的备用接口。ppp 的含义同标准外设驱动                                                        |
| stm32f1xx_II_ppp.c <br>stm32f1xx_II_ppp..h                                                             | LL 库文件，在一些复杂外设中实现底层功能，在部分 stm32f1xx_hal_ppp.c 中被调用                                                                                                        |

## naming rules of stm32f1xx_hal_ppp (c/h)

- init/deinit反初始化 function: :HAL_PPP_Init(), HAL_PPP_DeInit()

- Peripheral外设 Read/Write function :HAL_PPP_Read(),HAL_PPP_Write(),HAL_PPP_Transmit(),
  HAL_PPP_Receive()

- control func : HAL_PPP_Set (),HAL_PPP_Get ()

- get state/ error : HAL_PPP_GetState (), HAL_PPP_GetError ().

## common register operations

| 宏定义结构                                               | function  |
| --------------------------------------------------- | --------- |
| __HAL_PPP_ENABLE_IT(**HANDLE**, **INTERRUPT**)      | 使能外设中断    |
| __HAL_PPP_DISABLE_IT(**HANDLE**,**INTERRUPT**)      | 禁用外设中断    |
| __HAL_PPP_GET_IT (**HANDLE**, __ INTERRUPT __)      | 获取外设某一中断源 |
| __HAL_PPP_CLEAR_IT (**HANDLE**, __ INTERRUPT __)    | 清除外设中断    |
| __HAL_PPP_GET_FLAG (**HANDLE**, **FLAG**)           | 获取外设的状态标记 |
| __HAL_PPP_CLEAR_FLAG (**HANDLE**, **FLAG**)         | 清除外设的状态标记 |
| __HAL_PPP_ENABLE(**HANDLE**)                        | 使能某一外设    |
| __HAL_PPP_DISABLE(**HANDLE**)                       | 禁用某一外设    |
| __HAL_PPP_XXXX (**HANDLE**, **PARAM**)              | 针对外设的特殊操作 |
| *_HAL_PPP_GET* IT_SOURCE (**HANDLE**，__INTERRUPT _） | 检查外设的中断源  |

However, for core peripherals or shared resources such as SYSTICK/NVIC/RCC/FLASH/GPIO, control is not performed using peripherals handles such as PPP_HandleTypedef.

## Callback function回调函数

> what's the callback function:
> 
>     the function that is passed to other functons as an arg

| 回调函数                                                                                                | 举例                                                                                              |
| --------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| HAL_PPP_MspInit() / _DeInit()<br/>由 HAL_PPP_Init()这个 API 调用，主要在这个函数中实现外设对应的 GPIO、时钟、DMA、中断开启的配置和操作。 | 举例: HAL_USART_MspInit()                                                                         |
| HAL_PPP_ProcessCpltCallback                                                                         | 举例: HAL_USART_TxCpltCallback<br/>由外设中断或 DMA 中断调用，调用时 API 内部已经实现中 断标记的清除的操作，用户只需要专注于自己的软件功能实现即可。 |
| HAL_PPP_ErrorCallback<br/>外设或 DMA 中断中发生的错误，用于作错误处理。                                                 | 举例: HAL_USART_ErrorCallback                                                                     |

# learn to use HAL lib to organize toolchain工具链

## the software developling process for embedded systems typically总是 follows these steps

- organize toolchain

- write code

- generate .exe using HAL lib application files 用HAL库应用程序文件生成可执行文件

- flash the .exe to the chip烧录可执行文件到芯片

- execute the .exe on the chip

## HAL lib application file structure

:w<img title="" src="./img/2024-06-03-14-19-27-image.png" alt="" data-align="inline">

![](../_resources/2024-06-03-14-20-20-image.png)

## user configuration file——stm32f1xx_hal_conf.h

The official doesn't directly provide the file, but we can create one based on STM32Cube_FW_F1_V1.8.3\Drivers\STM32F1xx_HAL_Driver\Inc\《stm32f1xx_hal_conf_template.h》

The configure file of my board is in STM32Cube_FW_F1_V1.8.3\Projects\STM3210E_EVAL\Templates\Inc

- ##### configure the frequency of the external high-speed crystal oscillator晶振
  
  - HSE_VALUE = the frequency of an external high-speed crystal oscillator.We can configure it using the following code
    
    ```c
    #if !defined (HSE_VALUE)
    #if defined(USE_STM3210C_EVAL)    # USE_STM3210C_EVAL need to be configured
    #define HSE_VALUE
    25000000U /*!< Value of the External oscillator in Hz */
    #else
    #define HSE_VALUE
    8000000U /*!< Value of the External oscillator in Hz */
    #endif
    #endif /* HSE_VALUE */
    ```
  
  - or 
    
    - ```c
      #define HSE_VALUE
      8000000U /*!< Value of the External oscillator in Hz */
      ```

- ###### configure the frequency of the external low-speed crystal oscilator(RTC frequency)
  
  - LSE_VALUE. 
    
    - ```c
      #if !defined (LSE_VALUE)
      #define LSE_VALUE
      ((uint32_t)32768) /* 外部低速振荡器的值,单位 HZ */
      #endif /* LSE_VALUE */
      ```

- ###### enable/disable modules
  
  - the following code is in row 31\~71. If you want to disable a module, just comment out the line
    
    - ```c
      /* ########################## Module Selection #############################
      */
      /**
      * @brief This is the list of modules to be used in the HAL driver
      */
      #define HAL_MODULE_ENABLED
      #define HAL_ADC_MODULE_ENABLED
      #define HAL_CEC_MODULE_ENABLED
      #define HAL_COMP_MODULE_ENABLED
      #define HAL_CORTEX_MODULE_ENABLED
      ...中间省略...
      #define HAL_USART_MODULE_ENABLED
      #define HAL_WWDG_MODULE_ENABLED
      #define HAL_MMC_MODULE_ENABLED
      ```

- ###### configure the tick interrupt priority 滴答计时器优先级
  
  - ```c
    #define TICK_INT_PRIORITY    ((uint32_t)0x0F) /*!< tick interrupt priority*/
    ```

## stm32f1xx_hal.c 文件

- ##### HAL_Init()
  - the init function of the HAL, it must be called first in the programc

```c
HAL_StatusTypeDef HAL_Init(void) 
{ 
  /* 配置 Flash 的预取控制器 */ 
#if (PREFETCH_ENABLE != 0) 
#if defined(STM32F101x6) || defined(STM32F101xB) || defined(STM32F101xE) || 
defined(STM32F101xG) || \ defined(STM32F102x6) || defined(STM32F102xB) || \ 
    defined(STM32F103x6) || defined(STM32F103xB) || defined(STM32F103xE) || 
defined(STM32F103xG) || \ defined(STM32F105xC) || defined(STM32F107xC) 

  /* Prefetch buffer is not available on value line devices */ 
  __HAL_FLASH_PREFETCH_BUFFER_ENABLE(); 
#endif 
#endif /* 使能 Flash 的预取控制器 */ 

  /* 配置中断优先级顺序 */ 
  HAL_NVIC_SetPriorityGrouping(NVIC_PRIORITYGROUP_4); 

  /* 使用滴答定时器作为时钟基准，配置 1ms 滴答（重置后默认的时钟源为 HSI） */ 
  HAL_InitTick(TICK_INT_PRIORITY); 

  /* 初始化其它底层硬件（如果必要） */
  HAL_MspInit(); 
  /* 返回函数状态 */ 
  return HAL_OK; 
} 
```

- ##### HAL_MspInit
  
  - used to init the low-level hardwares
  
  > For convinence and compatibility兼容性, the interrupt priority group of the ZDAtom HAL library expamples is set to group 2, which means chaning "HAL_NVIC_SetPriorityGrouping(NVIC_PRIORITYGROUP_4)" to "HAL_NVIC_SetPriorityGrouping(NVIC_PRIORITYGROUP_2); "
  > 
  > interrupt priority = 2 ==> responses priority = 2

- ##### HAL_DeInit ()
  
  - it's a optional func可选函数
  
  - ```c
    HAL_StatusTypeDef HAL_DeInit(void) 
    { 
      /* 重置所有外设 */ 
      __HAL_RCC_APB1_FORCE_RESET(); 
      __HAL_RCC_APB1_RELEASE_RESET(); 
    
      __HAL_RCC_APB2_FORCE_RESET(); 
      __HAL_RCC_APB2_RELEASE_RESET(); 
    
      /* 对底层硬件初始化 */ 
      HAL_MspDeInit(); 
    
      /* 返回函数状态 */ 
      return HAL_OK; 
    } 
    ```

- ##### HAL_InitTick ()
  
  - ```c
    __weak HAL_StatusTypeDef HAL_InitTick(uint32_t TickPriority) 
    { 
      /* uwTickFreq 是个枚举类型，如果检测到 uwTickFreq 为零，则返回 */
    if((uint32_t)uwTickFreq == 0UL) 
      { 
        return HAL_ERROR; 
      } 
    
        /* 配置滴答定时器 1ms 产生一次中断 */ 
      if (HAL_SYSTICK_Config(SystemCoreClock /(1000UL / (uint32_t)uwTickFreq))> 
    0U) 
      { 
        return HAL_ERROR; 
      } 
    
      /* 配置滴答定时器中断优先级 */ 
      if (TickPriority < (1UL << __NVIC_PRIO_BITS)) 
      { 
        HAL_NVIC_SetPriority(SysTick_IRQn, TickPriority, 0U); 
        uwTickPrio = TickPriority; 
      } 
      else 
      { 
        return HAL_ERROR; 
      } 
    
      /* 返回函数状态 */ 
      return HAL_OK; 
    } 
    ```

- ##### Tick-timer related functions
  
  - ```c
    /* 该函数在滴答定时器时钟中断服务函数中被调用，函数每 1ms 让全局变量 uwTick 计数值加 1 */ 
      __weak void HAL_IncTick(void) 
      { 
        uwTick += (uint32_t)uwTickFreq; 
      } 
    
      /* 获取全局变量 uwTick 当前计算值 */ 
      __weak uint32_t HAL_GetTick(void) 
      { 
        return uwTick; 
      } 
    
      /* 获取滴答时钟优先级 */ 
      uint32_t HALGetTickPrio(void) 
      { 
        return uwTickPrio; 
      }  
      /* 设置滴答定时器中断频率 */ 
      HAL_StatusTypeDef HAL_SetTickFreq(HAL_TickFreqTypeDef Freq) 
      { 
        HAL_StatusTypeDef status  = HAL_OK; 
        HAL_TickFreqTypeDef prevTickFreq; 
    
        assert_param(IS_TICKFREQ(Freq)); 
    
        if (uwTickFreq != Freq) 
        { 
    
          /* 备份滴答定时器中断频率 */ 
          prevTickFreq = uwTickFreq; 
    
          /* 更新被 HAL_InitTick()调用的全局变量 uwTickFreq */ 
          uwTickFreq = Freq; 
    
          /* 应用新的滴答定时器中断频率  */ 
          status = HAL_InitTick(uwTickPrio); 
          if (status != HAL_OK) 
          { 
            /* 恢复之前的滴答定时器中断频率 */ 
            uwTickFreq = prevTickFreq; 
          } 
        } 
    
        return status; 
      } 
    
      /* 获取滴答定时器中断频率 */ 
      HAL_TickFreqTypeDef HAL_GetTickFreq(void) 
      { 
        return uwTickFreq; 
      } 
    
      /*HAL 库的延时函数，默认延时单位 ms  */ 
      __weak void HAL_Delay(uint32_t Delay) 
      { 
        uint32_t tickstart = HAL_GetTick(); 
        uint32_t wait = Delay; 
    
        /* Add a freq to guarantee minimum wait */ 
        if (wait < HAL_MAX_DELAY) 
        { 
          wait += (uint32_t)(uwTickFreq); 
        } 
    
        while ((HAL_GetTick() - tickstart) < wait) 
        { 
        } 
      } 
    
      /* 挂起滴答定时器中断，全局变量 uwTick 计数停止 */ 
      __weak void HAL_SuspendTick(void) 
      { 
        /* 禁止滴答定时器中断 */ 
        CLEAR_BIT(SysTick->CTRL, SysTick_CTRL_TICKINT_Msk); 
      } 
    
      /* 恢复滴答定时器中断，恢复全局变量 uwTick 计数 */ 
      __weak void HAL_ResumeTick(void) 
      { 
        /* 使能滴答定时器中断 */ 
        SET_BIT(SysTick->CTRL, SysTick_CTRL_TICKINT_Msk); 
      }
    ```

- ##### HAL library version related functions
  
  - ```c
    uint32_t HAL_GetHalVersion(void);  /* 获取 HAL 库驱动程序版本 */ 
    uint32_t HAL_GetREVID(void);       /* 获取设备修订标识符 */ 
    uint32_t HAL_GetDEVID(void);       /* 获取设备标识符 */ 
    uint32_t HAL_GetUIDw0(void);       /* 获取唯一设备标识符的第一个字 */ 
    uint32_t HAL_GetUIDw1(void);       /* 获取唯一设备标识符的第二个字 */ 
    uint32_t HAL_GetUIDw2(void);       /* 获取唯一设备标识符的第三个字 */ 
    ```

## Interrupt handling

- ##### What's the interrput
  
  - STM32 stop current running processes to handle more important processes. These "more important processes" are defined by developers in the software

## 

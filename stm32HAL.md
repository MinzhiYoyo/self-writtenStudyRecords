# 下载程序

> isp下载
>
> - DTR低电平复位，RTS高电平进BootLoader  
> - RTS高电平复位，DTR高电平进BootLoader 
>
> Jlink下载
>
> stlink下载

# stm32cunbemx初始化

> 1、配置SYS
>
> - 如果需要使用jlink下载或者调试，那就点击 Debug --> Serial Wire
> - 如果使用到了RTOS，Timebase Sourse -->  非SysTick的其他空闲定时器
>
> 2、配置RCC时钟
>
> - High Speed Clock（HSE）--> Crystal/Ceramic Resonator（必选）
> - Low Speed Clock（LSE）--> Crystal/Ceramic Resonator（非必选）
>
> 3、点击上方Clock Configuration配置时钟树
>
> - 使能HSE和PLLCLK即可
> - 时钟分频根据需要进行调节

# GPIO使用

> 注意事项：
>
> - 如果是用于测量信号的，可以适当选择下拉电阻
> - 可以自己定义引脚名称
> - 一般选择推挽输出

### 重要函数

```c
/*读取某个引脚上的电平*/
GPIO_PinState HAL_GPIO_ReadPin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin);
/*翻转电平*/
void HAL_GPIO_TogglePin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin);
/*写入电平*/ 
void HAL_GPIO_WritePin(GPIO_TypeDef *GPIOx, uint16_t GPIO_Pin, GPIO_PinState PinState); 

/*其中 GPIO_PinState定义如下*/
typedef enum
{
  GPIO_PIN_RESET = 0u,
  GPIO_PIN_SET
} GPIO_PinState;
```

	# 定时器的使用

> 要点：
>
> - 定时器有4个通道（通常来说）
>
> - 通道用处，PWM 输出，输入捕获等等
>
> - 参数：
>
>   > Prescaler：预分频系数
>   >
>   > - 系统时钟给 TIM 时钟，那么预分频系数为多少，就系统时钟分频多少给 TIM 。
>   > - 假如系统时钟为16M，预分频系数设置为16-1，那么TIM时钟就是1M
>   > - 定时器要么向上计数，要么向下计数
>   > - TIM时钟频率就是定时器以该频率计数，即1M频率=计一个数需要1us
>   >
>   > Period：计数最大值
>   >
>   > - 如果从0开始计数，数到Period就溢出了，就可以进入中断并清零
>
> - cubemx基本参数：
>
>   > - Prescaler ：见上
>   >
>   > - Counter Mode：计数模式，向上计数或者向下计数
>   >
>   > - Period：见上
>   >
>   > - Internal Clock Division：是否分频
>   >
>   > - RCR：
>   >
>   > - auto-reload preload：溢出自动重装载
>   >
>   >   输入捕获模式参数：
>   >
>   >   - Polarity Selection：上升沿触发或者下降沿触发
>   >   - IC Selection：
>   >   - Prescaler Division Ratio：分频
>   >   - Input Filter（4 bits value）：
>   >
>   >   PWM 参数：
>   >
>   >   - Mode ：模式1是计数值小于于Pulse为高电平，大于Pulse为低电平；模式2反之
>   >   - Pulse ：见上
>   >   - Output compare prelo：
>   >   - Fast Mode：
>   >   - CH Polarity：占空比为高电平还是低电平
>
> - 中断的参数
>
>   > TIM break interrupt：
>   >
>   > TIM update interrupt：定时器溢出中断 
>   >
>   > TIM trigger and commutation interrupts：
>   >
>   > TIM capture compare interrup：输入捕获中断

### 重要函数

```c

```


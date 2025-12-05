# 代码示例

本文档提供了 STM32F103C8T6 的常用代码示例。

## 目录

- [GPIO 输出 - LED 闪烁](#gpio-输出---led-闪烁)
- [GPIO 输入 - 按钮检测](#gpio-输入---按钮检测)
- [延时函数](#延时函数)
- [UART 串口通信](#uart-串口通信)
- [定时器](#定时器)

---

## GPIO 输出 - LED 闪烁

最基础的示例：控制 LED 闪烁。

### 硬件连接
- LED 连接到 PC13（STM32F103C8T6 蓝色 LED）
- 通过电阻连接到 GND

### 代码示例

**Inc/gpio_example.h**
```c
#ifndef GPIO_EXAMPLE_H
#define GPIO_EXAMPLE_H

#include <stdint.h>

// GPIO 寄存器定义
#define RCC_BASE        0x40021000
#define GPIOC_BASE      0x40011000

#define RCC_APB2ENR     (*(volatile uint32_t *)(RCC_BASE + 0x18))
#define GPIOC_CRH       (*(volatile uint32_t *)(GPIOC_BASE + 0x04))
#define GPIOC_ODR       (*(volatile uint32_t *)(GPIOC_BASE + 0x0C))

// RCC 时钟使能位
#define RCC_APB2ENR_IOPCEN  (1 << 4)

// GPIO 引脚
#define GPIO_PIN_13     (1 << 13)

void gpio_init(void);
void led_on(void);
void led_off(void);
void led_toggle(void);
void delay_ms(uint32_t ms);

#endif // GPIO_EXAMPLE_H
```

**Src/gpio_example.c**
```c
#include "gpio_example.h"

void gpio_init(void)
{
    // 使能 GPIOC 时钟
    RCC_APB2ENR |= RCC_APB2ENR_IOPCEN;

    // 配置 PC13 为推挽输出模式，最大速度 2MHz
    // CNF13[1:0] = 00 (推挽输出)
    // MODE13[1:0] = 10 (输出模式，最大速度 2MHz)
    GPIOC_CRH &= ~(0xF << 20);  // 清除 PC13 配置位
    GPIOC_CRH |= (0x2 << 20);   // 设置为输出模式
}

void led_on(void)
{
    GPIOC_ODR &= ~GPIO_PIN_13;  // PC13 低电平，LED 亮
}

void led_off(void)
{
    GPIOC_ODR |= GPIO_PIN_13;   // PC13 高电平，LED 灭
}

void led_toggle(void)
{
    GPIOC_ODR ^= GPIO_PIN_13;   // 翻转 PC13 状态
}

void delay_ms(uint32_t ms)
{
    // 简单的延时函数（不精确）
    // 假设系统时钟为 8MHz
    for(uint32_t i = 0; i < ms; i++) {
        for(uint32_t j = 0; j < 1000; j++) {
            __asm__("nop");
        }
    }
}
```

**修改 Src/main.c**
```c
#include <stdint.h>
#include "gpio_example.h"

int main(void)
{
    gpio_init();

    while(1) {
        led_toggle();
        delay_ms(500);
    }
}
```

---

## GPIO 输入 - 按钮检测

读取按钮状态，控制 LED。

**Inc/button_example.h**
```c
#ifndef BUTTON_EXAMPLE_H
#define BUTTON_EXAMPLE_H

#include <stdint.h>
#include <stdbool.h>

// GPIO 寄存器定义
#define GPIOA_BASE      0x40010800
#define GPIOA_CRL       (*(volatile uint32_t *)(GPIOA_BASE + 0x00))
#define GPIOA_IDR       (*(volatile uint32_t *)(GPIOA_BASE + 0x08))

#define RCC_APB2ENR_IOPAEN  (1 << 2)

// 按钮引脚 PA0
#define BUTTON_PIN      (1 << 0)

void button_init(void);
bool button_is_pressed(void);

#endif // BUTTON_EXAMPLE_H
```

**Src/button_example.c**
```c
#include "button_example.h"
#include "gpio_example.h"

void button_init(void)
{
    // 使能 GPIOA 时钟
    RCC_APB2ENR |= RCC_APB2ENR_IOPAEN;

    // 配置 PA0 为上拉输入
    // CNF0[1:0] = 10 (上拉/下拉输入)
    // MODE0[1:0] = 00 (输入模式)
    GPIOA_CRL &= ~(0xF << 0);   // 清除 PA0 配置位
    GPIOA_CRL |= (0x8 << 0);    // 设置为上拉输入
}

bool button_is_pressed(void)
{
    // 按钮按下时 PA0 为低电平
    return !(GPIOA_IDR & BUTTON_PIN);
}
```

**使用示例**
```c
#include <stdint.h>
#include "gpio_example.h"
#include "button_example.h"

int main(void)
{
    gpio_init();
    button_init();

    while(1) {
        if(button_is_pressed()) {
            led_on();
        } else {
            led_off();
        }
        delay_ms(10);  // 消抖延时
    }
}
```

---

## 延时函数

更精确的延时函数，使用 SysTick 定时器。

**Inc/systick_delay.h**
```c
#ifndef SYSTICK_DELAY_H
#define SYSTICK_DELAY_H

#include <stdint.h>

void systick_init(void);
void delay_ms(uint32_t ms);
void delay_us(uint32_t us);
uint32_t millis(void);

#endif // SYSTICK_DELAY_H
```

**Src/systick_delay.c**
```c
#include "systick_delay.h"

// SysTick 寄存器
#define SYSTICK_BASE    0xE000E010
#define SYSTICK_CTRL    (*(volatile uint32_t *)(SYSTICK_BASE + 0x00))
#define SYSTICK_LOAD    (*(volatile uint32_t *)(SYSTICK_BASE + 0x04))
#define SYSTICK_VAL     (*(volatile uint32_t *)(SYSTICK_BASE + 0x08))

static volatile uint32_t system_ticks = 0;

void systick_init(void)
{
    // 配置 SysTick 为 1ms 中断
    // 假设系统时钟为 8MHz
    SYSTICK_LOAD = 8000 - 1;        // 8MHz / 8000 = 1kHz (1ms)
    SYSTICK_VAL = 0;                 // 清除当前值
    SYSTICK_CTRL = 0x07;             // 使能，中断，使用处理器时钟
}

void SysTick_Handler(void)
{
    system_ticks++;
}

void delay_ms(uint32_t ms)
{
    uint32_t start = system_ticks;
    while((system_ticks - start) < ms);
}

void delay_us(uint32_t us)
{
    // 粗略的微秒延时（8MHz 时钟）
    for(uint32_t i = 0; i < us * 2; i++) {
        __asm__("nop");
    }
}

uint32_t millis(void)
{
    return system_ticks;
}
```

---

## UART 串口通信

通过 UART1 发送和接收数据。

**Inc/uart_example.h**
```c
#ifndef UART_EXAMPLE_H
#define UART_EXAMPLE_H

#include <stdint.h>

void uart_init(uint32_t baudrate);
void uart_send_char(char ch);
void uart_send_string(const char *str);
char uart_receive_char(void);

#endif // UART_EXAMPLE_H
```

**Src/uart_example.c**
```c
#include "uart_example.h"
#include "gpio_example.h"

// UART1 寄存器定义
#define USART1_BASE     0x40013800
#define USART1_SR       (*(volatile uint32_t *)(USART1_BASE + 0x00))
#define USART1_DR       (*(volatile uint32_t *)(USART1_BASE + 0x04))
#define USART1_BRR      (*(volatile uint32_t *)(USART1_BASE + 0x08))
#define USART1_CR1      (*(volatile uint32_t *)(USART1_BASE + 0x0C))

// USART 状态寄存器位
#define USART_SR_TXE    (1 << 7)
#define USART_SR_RXNE   (1 << 5)

// USART 控制寄存器位
#define USART_CR1_UE    (1 << 13)
#define USART_CR1_TE    (1 << 3)
#define USART_CR1_RE    (1 << 2)

// RCC APB2 外设时钟使能
#define RCC_APB2ENR_USART1EN  (1 << 14)

void uart_init(uint32_t baudrate)
{
    // 使能 USART1 和 GPIOA 时钟
    RCC_APB2ENR |= RCC_APB2ENR_USART1EN | RCC_APB2ENR_IOPAEN;

    // 配置 PA9 (TX) 为复用推挽输出
    GPIOA_CRH &= ~(0xF << 4);
    GPIOA_CRH |= (0xB << 4);    // 复用推挽输出，50MHz

    // 配置 PA10 (RX) 为浮空输入
    GPIOA_CRH &= ~(0xF << 8);
    GPIOA_CRH |= (0x4 << 8);    // 浮空输入

    // 配置波特率 (假设 APB2 时钟为 8MHz)
    // BRR = APB2_CLK / baudrate
    USART1_BRR = 8000000 / baudrate;

    // 使能 USART1，发送和接收
    USART1_CR1 = USART_CR1_UE | USART_CR1_TE | USART_CR1_RE;
}

void uart_send_char(char ch)
{
    // 等待发送缓冲区为空
    while(!(USART1_SR & USART_SR_TXE));
    USART1_DR = ch;
}

void uart_send_string(const char *str)
{
    while(*str) {
        uart_send_char(*str++);
    }
}

char uart_receive_char(void)
{
    // 等待接收到数据
    while(!(USART1_SR & USART_SR_RXNE));
    return (char)USART1_DR;
}
```

**使用示例**
```c
#include <stdint.h>
#include "uart_example.h"
#include "systick_delay.h"

int main(void)
{
    systick_init();
    uart_init(115200);

    uart_send_string("STM32F103C8T6 UART Example\r\n");

    while(1) {
        uart_send_string("Hello, World!\r\n");
        delay_ms(1000);
    }
}
```

---

## 定时器

使用 TIM2 产生 1Hz 的定时中断。

**Inc/timer_example.h**
```c
#ifndef TIMER_EXAMPLE_H
#define TIMER_EXAMPLE_H

#include <stdint.h>

void timer2_init(void);

#endif // TIMER_EXAMPLE_H
```

**Src/timer_example.c**
```c
#include "timer_example.h"
#include "gpio_example.h"

// TIM2 寄存器定义
#define TIM2_BASE       0x40000000
#define TIM2_CR1        (*(volatile uint32_t *)(TIM2_BASE + 0x00))
#define TIM2_DIER       (*(volatile uint32_t *)(TIM2_BASE + 0x0C))
#define TIM2_SR         (*(volatile uint32_t *)(TIM2_BASE + 0x10))
#define TIM2_PSC        (*(volatile uint32_t *)(TIM2_BASE + 0x28))
#define TIM2_ARR        (*(volatile uint32_t *)(TIM2_BASE + 0x2C))

// NVIC 寄存器
#define NVIC_ISER0      (*(volatile uint32_t *)0xE000E100)

// RCC APB1 外设时钟使能
#define RCC_APB1ENR     (*(volatile uint32_t *)(RCC_BASE + 0x1C))
#define RCC_APB1ENR_TIM2EN  (1 << 0)

// TIM2 控制位
#define TIM_CR1_CEN     (1 << 0)
#define TIM_DIER_UIE    (1 << 0)
#define TIM_SR_UIF      (1 << 0)

void timer2_init(void)
{
    // 使能 TIM2 时钟
    RCC_APB1ENR |= RCC_APB1ENR_TIM2EN;

    // 配置预分频器和自动重装载值
    // 假设 APB1 时钟为 8MHz
    // 预分频 8000 -> 1kHz
    // 自动重装载 1000 -> 1Hz
    TIM2_PSC = 8000 - 1;
    TIM2_ARR = 1000 - 1;

    // 使能更新中断
    TIM2_DIER |= TIM_DIER_UIE;

    // 使能 TIM2 全局中断 (IRQ 28)
    NVIC_ISER0 |= (1 << 28);

    // 启动定时器
    TIM2_CR1 |= TIM_CR1_CEN;
}

// TIM2 中断处理函数
void TIM2_IRQHandler(void)
{
    if(TIM2_SR & TIM_SR_UIF) {
        // 清除中断标志
        TIM2_SR &= ~TIM_SR_UIF;

        // 在这里执行定时任务
        led_toggle();
    }
}
```

**修改 Src/startup_stm32f103xx.S**

在中断向量表中添加 TIM2_IRQHandler（如果尚未添加）。

**使用示例**
```c
#include <stdint.h>
#include "gpio_example.h"
#include "timer_example.h"

int main(void)
{
    gpio_init();
    timer2_init();

    // LED 将由定时器中断控制，1Hz 闪烁
    while(1) {
        // 主循环可以做其他事情
    }
}
```

---

## 注意事项

1. **时钟配置**
   - 以上示例假设使用内部 8MHz HSI 时钟
   - 如果使用外部晶振或 PLL，需要相应调整波特率和定时器参数

2. **寄存器访问**
   - 示例使用寄存器直接操作，更底层但更灵活
   - 实际项目可以考虑使用 STM32 HAL 或 LL 库

3. **中断优先级**
   - 示例未设置中断优先级
   - 多个中断时应配置 NVIC 优先级

4. **添加到项目**
   - 将头文件放在 `Inc/` 目录
   - 将源文件放在 `Src/` 目录
   - 在 `CMakeLists.txt` 中添加新的源文件

5. **硬件连接**
   - 确保正确连接外部设备（LED、按钮、UART 等）
   - 注意电压电平和电流限制

## 相关资源

- STM32F103 参考手册：详细的寄存器说明
- STM32F103 数据手册：引脚定义和电气特性
- ARM Cortex-M3 技术手册：内核和系统外设

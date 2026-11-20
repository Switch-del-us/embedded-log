# 03 · 串口：环形缓冲 + 空闲中断 + DMA

## 目标

不定长收包：不知道对方发几个字节，也能完整收下一帧并交给上层解析。

## 为什么不用 `HAL_UART_Receive`

`HAL_UART_Receive` 要预先知道长度，而且阻塞。真实设备（PLC、传感器）发的是变长帧，
所以要用 **DMA + 空闲中断（IDLE）**：DMA 负责搬数据，IDLE 中断告诉你"这帧发完了"。

## CubeMX 配置

| 项目 | 配置 |
|---|---|
| USART1 | 115200, 8N1, 开启全局中断 |
| DMA | USART1_RX，Circular 模式 |
| NVIC | USART1 中断使能，优先级低于定时器 |

## 实现要点

1. DMA 循环模式接收，`__HAL_UART_ENABLE_IT(&huart1, UART_IT_IDLE)`
2. IDLE 中断里：先读 `SR` 再读 `DR` 清标志（顺序错了清不掉），算出这帧长度
3. 长度 = 缓冲区总长 - `__HAL_DMA_GET_COUNTER(&hdma_usart1_rx)`
4. 数据拷进环形缓冲，主循环从环形缓冲取帧解析

## printf 重定向（Keil / ARMCC）

```c
#include <stdio.h>
int fputc(int ch, FILE *f) {
    HAL_UART_Transmit(&huart1, (uint8_t *)&ch, 1, HAL_MAX_DELAY);
    return ch;
}
```

## 踩的坑

<!-- 典型：忘了开 IDLE 中断、清标志顺序错、DMA 半满中断和 IDLE 打架、波特率误差导致偶发乱码 -->

## 现象

<!-- 串口助手截图：发 5 字节、20 字节、1 字节都能完整收到 -->

# project-modbus · 多路数据采集 + Modbus RTU 从机

W9–W16 的主项目。目标：做一块自己画的板子，实现工业现场最常见的"采集 + 通信"组合，
对标 PLC 的模拟量扩展模块。

## 功能

- 4 路模拟量采集（0–3.3V / 4–20mA 可选），误差 ≤ ±0.5%
- Modbus RTU 从机，支持功能码 03（读保持寄存器）/ 06（写单个）/ 16（写多个）
- 参数掉电保存（Flash 模拟 EEPROM），带校验
- 独立看门狗 + 复位原因记录
- 上位机：Python + pyserial + matplotlib 实时曲线

## 目录

```
project-modbus/
├── hardware/        # 原理图 PDF、PCB 截图、BOM
├── firmware/        # 固件工程
│   ├── bsp/         # 板级驱动：adc, uart, flash, wdg
│   ├── app/         # 业务：采集调度、校准、参数管理
│   └── protocol/    # Modbus 协议栈
├── host/            # Python 上位机
└── doc/             # 需求文档、调试记录、演示视频
```

## 进度

- [ ] W9  需求文档 + 系统框图 + BOM
- [ ] W10 原理图
- [ ] W11 PCB 投板
- [ ] W12 焊接与硬件调试
- [ ] W13 采集功能（ADC + DMA + 校准）
- [ ] W14 Modbus RTU 从机
- [ ] W15 工程化收尾（Flash 存储 / 看门狗 / 上位机）
- [ ] W16 文档与演示视频

## 这一版的设计取舍

<!-- 做完后回来补：为什么选这颗 MCU、为什么 RS485 要隔离、为什么用外部基准而不是 VDDA -->

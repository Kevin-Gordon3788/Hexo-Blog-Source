---
title: Microchip-按键输入
tags:
  - 单片机
  - 按键输入
abbrlink: 3413069295
date: 2020-07-01 23:08:04
---

对于单片机来说，按键也是一种比较重要的人机交互方式，我们通过代码来让按键控制我们的LED

# 硬件原理图

如图可知，当没按下去的时候，MCU端口识别为高电平，当按下了，MCU端口识别为低电平，其中按键为RC1 RC2 RC11

# 代码编写

![按键原理图](按键原理图.png)

![MCU原理图](MCU原理图.png)

#define TRISC_INIT      0xfc37 //RC1 RC2 RC11 设置为输入
#define PORTC_INIT      0x0000
#define LATC_INIT       0x0000
#define ODCC_INIT       0x0000
#define CNENC_INIT      0x0000
#define CNPUC_INIT      0x0000
#define CNPDC_INIT      0x0000
#define ANSELC_INIT     0x0000

void keyInit()

{

TRISC = TRISC_INIT;
PORTC = PORTC_INIT;
LATC = LATC_INIT;
ODCC = ODCC_INIT;
CNENC = CNENC_INIT;
CNPUC = CNPUC_INIT;
CNPDC = CNPDC_INIT;
ANSELC = ANSELC_INIT;

}

# 按键消抖

一般使用按键功能，都需要进行消抖的操作，以免在单片机工作过程中，如果遇到了未知的干扰，出现不可预知的状态

而我们一般使用延时做消抖

设计思路如下：按键有效 - 延时一段时间 - 判断按键是否仍然有效 -如果有效则认为本次按键按下了，否则则没按下




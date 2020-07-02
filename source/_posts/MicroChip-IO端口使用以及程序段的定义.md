---
title: MicroChip IO端口使用以及程序段的定义
tags:
  - 单片机
  - IO端口
abbrlink: 764096901
date: 2020-06-30 22:32:35
---

单片机程序的编写，离不开IO的使用，比如在调试的时候需要翻转IO来测试时钟，或者用IO来进行调试等等

# IO端口的概念

## 高阻态

​	在单片机中，可能存在一些IO端口没有被使用的情况，这个时候IO不接高电平也不接低电平，也就是相当于端口处于悬空的状态，这个时候它的输入阻抗是很大的，消耗电流很小。我们称这种状态为高阻态

​	一般在单片机复位后，引脚会默认配置为输入状态，如果有使用不到的引脚，会把它设置为输入。这样会减小一部分芯片功耗，在一定程序上也会增加芯片抗干扰能力

## 上拉/下拉

​	IO端口会处于悬空的状态，为了让端口具有确定的电平状态可能会在端口接一个电阻到VDD，我们称之为上拉。

​	也有可能在端口接一个电阻到GND，我们称之为下拉。

## 漏极开路输出

​	把MOSFET的漏极没有任何链接的输入状态称为漏极开路输出。这种漏极开路特性允许通过使用外部上拉电阻，在所需的任意5V耐压引脚上产生高于VDD（如5V）的输出电压。

![](漏极开路输出示例.png)



# 配置链接文件实现程序段的分配

我们打开工程里面的p33E128MC506.gld

假设我们需要为我们的程序配置段，我们需要做一下配置：

- 在链接文件中定义段属性

.led_section :
  {
        *(.led_section);
  } >program

- 定义段的声明的宏定义

#define     LED_SECTION    __attribute__((section(".led_section")))

- 函数使用宏定义来修饰

void LED_SECTION LedOff(void)
{

}



# 通过IO配置点亮LED灯

## 原理图查询

![](LED原理图.png)

![](LED原理图1.png)

## 硬件分析

当MCU的IO把端口设置为0的时候，电路存在压差，LED亮，否则，LED灭

电路存在向IO口灌电流的情况，但是IO的总灌电流的能力是有限的，需要控制灌电流的大小

# 软件编写

![I/O端口结构框图](IO端口结构框图.png)

- 引脚定义

LED0 - RE12 

LED1 - RE13 

LED2 - RE14 

LED3 - RE15

- 代码编写

初始化LED：

#define TRISE_INIT      0x0fff
#define PORTE_INIT      0xf000
#define LATE_INIT       0xf000
#define ODCE_INIT       0x0000
#define CNENE_INIT      0x0000
#define CNPUE_INIT      0x0000
#define CNPDE_INIT      0x0000
#define ANSELE_INIT     0x0000

TRISE = TRISE_INIT; //RE15 - RE12 作为输出功能
PORTE = PORTE_INIT;//初始化为高电平
LATE = LATE_INIT;//初始化为高电平
ODCE = ODCE_INIT; //不需要漏极开路输出
CNENE = CNENE_INIT;//不需要输入电平变化
CNPUE = CNPUE_INIT;//取消下拉
CNPDE = CNPDE_INIT;//取消上拉
ANSELE = ANSELE_INIT;//取消模拟输入



点亮LED：

先定义LED的端口

#define     LED0_PIN    LATEbits.LATE12
#define     LED1_PIN    LATEbits.LATE13
#define     LED2_PIN    LATEbits.LATE14
#define     LED3_PIN    LATEbits.LATE15

点亮LED：

LED0_PIN = LED1_PIN  = LED2_PIN = LED3_PIN  = 0；
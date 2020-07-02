---
title: Microchip-定时器的使用
tags:
  - 单片机
  - 定时器
abbrlink: 330686074
date: 2020-07-02 23:35:23
---

对于裸机系统而言，一般我们都是通过main函数 + 中断进行处理。

但是随着单片机的处理速度加强以及业务的不断增加，我们不可能整个单片机只运行一个任务。

要同时运行多个任务，我们就要对任务进行分时复用，不同的时间运行不同的任务。

要实现分时复用，我们需要有一个定时器

# 定时器框图

![定时器框图](定时器框图.png)

由定时器框图我们可知，要实现定时器中断，我们需要将PRX和TMRX进行比较，在向上计数模式下，如果PRX和TMRX相等，则触发中断，在向下计数下，当TMRX等于0，则触发中断

# 寄存器定义和代码编写

![数据手册](定时器配置数据手册.png)

- 配置前，我们需要先把定时器给关掉:T1CONbits.TON = 0;

- 把定时器分频设置为1:256:T1CON= 0x0030

  此时时钟为:240M/256。其中计数一次的时间为:1/(240M/256) = 4.27us

- 定时器初始值为0:TMR3 = 0x00
- 定时器周期为1ms,因为计数一次为4.27us，所以计算1ms需要计数0xEA次

- 清空定时器的中断标志位，使能定时器中断并且设置定时器的中断优先级

- 定义定时器的中断处理函数

void __attribute__((section(".main_section"),interrupt,shadow,no_auto_psv)) _T1Interrupt(void)
{
    IFS0bits.T1IF = 0;//清除定时器1的中断标志位
}

# 单片机如何实现任务的分时复用

有了单片机的定时器中断，我们就可以随意使用定时器来做任务的分配

- 定义几个变量: uint32_t timer100ms_1ms,timer1s_1ms;

- 在中断函数中不断对这几个变量进行自增

void __attribute__((section(".main_section"),interrupt,shadow,no_auto_psv)) _T1Interrupt(void)
{
    IFS0bits.T1IF = 0;//清除定时器1的中断标志位

​	timer100ms_1ms++；

​	timer1s_1ms++；

}

- 主函数里面对该变量进行处理

void main()

{

​	while(1)

​	{

​		if(timer100ms_1ms >= 100)

​		{

​			timer100ms_1ms  = 0;

​			task1();

​		}

​		if(timer1s_1ms >= 1000)

​		{

​			timer1s_1ms = 0;

​			task2();

​		}

​		

​	}

}








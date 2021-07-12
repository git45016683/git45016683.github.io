---
layout:     post
title:      STM32CubeMX创建MDK工程
subtitle:   手把手教程
date:       2021-04-27
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# STM32CubeMX创建MDK工程
1、选择MCU创建工程
![mcu](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/1-select-mcu.png)  

2、输入芯片型号，方便找到你的实际型号
![mcu-type](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/2-input-mcu-type.png)  

3、创建cubemx工程
![create-project](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/3-create-cubemx-project.png)  

4、配置sys
![sys](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/4-config-sys.png)  

5、配置RCC
![rcc](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/5-config-rcc.png)  

6、配置一个串口，作为调试窗口，方便输出调试信息
![debug-uart](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/6-config-debug-uart.png)  

7、配置时钟，最右边的在配置完前面3步后可自动生成，如有不是预期的，可进行微调
![clock](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/7-config-clock.png)  

8、配置工程
![config-project-1](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/8-config-project-1.png)  
![config-project-2](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/8-config-project-2.png)  

9、生成代码工程
![create](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/9-create-mdk.png)  
![open](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/9-open-mdk.png)  

10、打开生成的代码工程，编译通过
![build](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/10-build-pass.png)  

11、为printf输出到调试串口增加对应代码及头文件
![config-printf-1](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/11-config-printf-1.png)  
![config-printf-2](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/11-config-printf-2.png)  

12、添加测试验证串口调试信息代码，编译通过
![printf](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/12-test-printf.png)  

13、配置烧写/调试工具
![config-flash](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/13-config-flash.png)  

14、烧写程序成功，板子自动重启运行，调试信息已经通过串口输出。最简单的裸机程序done。
![flash-run](/img/frame/rt-thread/chapter0-stm32cubemx-create-mdk/14-flash-run.png)  


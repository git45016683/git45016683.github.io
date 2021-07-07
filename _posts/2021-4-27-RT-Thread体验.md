---
layout:     post
title:      RT-Thread入门体验
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-04-27
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 入门体验
##### 本入门体验示例，主要基于STM32CubeMX生成的工程，再手动移植RT-Thread源码的方式实现。关于如何使用STM32CubeMX创建对应工程，网上有很多教程，亦可参考blog内的教程《STM32CubeMX创建MDK工程》
1、新建存放rtt文件的文件夹，这里命名为rtos
![folder](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-1-folder.png)
2、将从rtt网站指导下下载下来的rtt-nano源码拷贝必要的文件夹到上一步新建的rtos文件夹
![copyfiles](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-2-copy-rtt-files.png)
bsp：板级支持包（当前示例stm32例子中只使用到board.c及rtconfig.h，其他可以删掉）
components：组件文件夹
include：头文件目录
libcpu：处理器相关的启动文件（stm32f103属于cortex-m3系列，只要保留cortex-m3文件夹即可，其他都可以删除）
src：内核源码
3、添加上述五个文件夹的所有内容到新建的rtos，并将其添加到git管控
![addtogit](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-3-add-to-git.png)
4、将上述描述到没有用到的芯片平台相关文件件和板级支持包删除，精简工程
![deletefiles](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-4-delete-needless-files.png)
5、将rtt相关的不需要修改的文件(除board.c和rtconfig.h外)都设置为只读属性，确保rtt相关的文件不会被误修改
![readonly](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-5-set-readonly.png)
6、添加rtt源文件到MDK工程
![addtomdk1](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-6-add-rttfiles-to-mdk-1.png)
![addtomdk2](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-6-add-rttfiles-to-mdk-2.png)
![addtomdk3](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-6-add-rttfiles-to-mdk-3.png)
![addtomdk4](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-6-add-rttfiles-to-mdk-4.png)
7、添加rtt的头文件到MDK工程
![addheadpath1](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-7-add-headfile-path-to-mdk-1.png)
![addheadpath2](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-7-add-headfile-path-to-mdk-2.png)
8、添加完文件后，编译一下。会提示重复定义的错误
![builderror](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-8-build-error.png)
![fixbuilderror1](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-8-fix-build-error-1.png)
![fixbuilderror2](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-8-fix-build-error-2.png)
删掉/注释掉/预编译原本stm32f10x_it.c的相关函数
![folder](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-1-folder.png)
9、解决重定义之后，编译成功
![buildpass](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-9-build-pass.png)
10、如果遇到找不到RTE_Components.h头文件的错误，则可以在rtconfig.h中注释掉这一行或者删掉
![rtecomponents](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-10-rte_components-headfile.png)
11、编译成功后烧写，只有一次串口信息输出。则需要修改使用rtt内定义的延时函数
![flash1](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-11-flash-1.png)
![flash2](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-11-flash-2.png)
12、添加rtt头文件包含
![includertt](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-12-include-rtt-headfile.png)
![callrttdelay](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-12-call-rtt-delay.png)
![callrttdelayflash](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-12-flash.png)
13、添加一个c文件方便统一创建线程
![mythreadfile](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-13-add-mythread-file.png)
14、将新增的头文件包含到主头文件
![includemythread](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-14-include-mythread-headfile.png)
15、动态创建线程函数声明
![dynamic1](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-15-call-dynamic-thread.png)
16、动态创建线程实现
![dynamic1](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-16-dynamic-create-thread.png)
17、动态创建线程调用
![calldynamicthread](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-17-call-dynamic-thread.png)
18、编译通过，烧写后线程正常启动，每隔1s输出一次调试信息
![rundynamicthread](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-18-dynamic-thread-run.png)
19、添加静态线程创建相关函数声明
![static](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-19-static-create-thread.png)
20、添加静态线程创建相关函数实现
![static1](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-20-static-create-thread.png)
21、静态线程函数调用
![callstaticthread](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-21-call-static-thread.png)
22、编译及烧写确认
![runstaticthread](/img/frame/rt-thread/chapter1-try-to-experience/RTT-1-22-static-thread-run.png)


<!-- ![404](/img/frame/404-bg.jpg) -->

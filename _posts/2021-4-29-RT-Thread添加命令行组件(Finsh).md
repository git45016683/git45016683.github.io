---
layout:     post
title:      RT-Thread添加命令行组件(Finsh)
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-04-29
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 添加Finsh组件
##### 参考链接：
> [移植控制台/FinSH (rt-thread.org)](https://www.rt-thread.org/document/site/#/rt-thread-version/rt-thread-nano/finsh-port/an0045-finsh-port)  

1、添加rt_hw_console_output(const char *str)控制台/串口输出函数的实现
(在usart.c文件中，基于CubeMx生成的工程-HAL库)
![rt_hw_console_output](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-1-add-rt_hw_console_output.png)  

2、使用rtt实现的rt_kprintf接口输出调试信息验证
![rt_kprintf](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-2-call-rk_kprintf.png)  

3、添加RTE_USING_FINSH宏定义，开启使用finsh组件
![RTE_USING_FINSH](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-3-add-RTE_USING_FINSH.png)  

4、实现finsh组件接受指令的函数rt_hw_console_getchar(void)
![rt_hw_console_getchar](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-4-rt_hw_console_getchar.png)  

5、编译，找不到finsh_api.h头文件，增加components/finsh目录到头文件路径
![not_find_header](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-5-add-finsh-head-path-1.png)  
![add_header_path](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-5-add-finsh-head-path-2.png)  

6、编译通过，烧写成功，finsh等待输入的msh>已显示
![flash](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-6-flash.png)  

7、测试：输入help，成功响应
![run](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-7-run-help.png)  

8、添加自建线程的指令到finsh
![custom_cmd](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-8-add-command.png)  

9、通过输入指令给finsh组件，启动线程
![run_custom_cmd](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-9-run-new-command.png)  

10、自动初始化(INIT_XX_EXPORT)
![auto_init](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-10-auto-init.png)  
xx共分五类：初始化顺序自上而下
> ①INIT_BOART_EXPORT      ： 板级自动初始化  
②INIT_PREV_EXPORT       ：组件自动预初始化可用  
③INIT_DEVICE_EXPORT     ：设备相关的自动初始化可用  
④INIT_COMPONENT_EXPORT  ：组件自动初始化可用  
⑤INIT_APP_EXPORT        ：应用层自动初始化可用  

PS:  
如上述图示的TaskCreate函数使用INIT_xxx_EXPORT自动初始化，会提示如下警告：
![auto_init_warning](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-10-auto-init-warning.png)  
主要是函数声明类型跟INIT_xxx_EXPORT要求的不一样，将函数返回值由void修改为int即可，当然函数内亦需要按实际情况return一个值。

<!-- ![404](/img/frame/404-bg.jpg) -->

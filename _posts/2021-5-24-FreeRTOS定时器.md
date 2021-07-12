---
layout:     post
title:      FreeRTOS定时器
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-24
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 定时器

1、添加定时器头文件
![include_head](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-1-include-head.png)  

2、动态创建定时器
![create_timer](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-2-create-timer-dynamic.png)  

3、静态创建定时器
![init_timer](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-3-init-timer-static.png)  

4、包含示例函数定义的mytimer.h头文件
![include_mytimer_head](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-4-include-mytimer-head.png)  

5、创建之
![create_timer_thread](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-5-create-timer-thread.png)  

6、编译……出错
![build_error](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-6-build-error.png)  

7、搜索上述未定义的函数，发现定时器相关的函数，受控于一个宏configUSE_TIMERS，该宏默认未开启
![macro_disable1](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-7-macro-disable-1.png)  
![macro_disable2](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-7-macro-disable-2.png)  

8、在freeRTOSConfig.h开启该宏configUSE_TIMERS
![macro_enable](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-8-macro-enable.png)  

9、再次编译……还有错
![build_error_2](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-9-build-error-again.png)  

10、根据提示，将宏configTIMER_TASK_PRIORITY也启用
![macro_enable_2](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-10-macro-disable.png)  

11、编译还有错，在同一个地方还有几个宏提示需要定义
![macro_disable_3](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-11-macro-disable.png)  

12、在FreeRTOSConfig.h启用相关宏定义
![macro_enable_3](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-12-macro-enable.png)  

13、编译，出错，依然提示有函数未定义 
![build_error_3](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-13-build-error-func-not-define.png)  

14、搜寻之下，发现该函数只有一个声明，并没有实现，添加一个空的实现函数后，编译通过
![define_func](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-14-define-null-func-build-pass.png)  

15、通过该函数来给(静态)定时器任务的任务堆 栈及任务控制块分配内存，添加对应的实现【只有使用了静态定时器才会需要该项，动态定时器不用】
![func_static](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-15-func.png)  

16、编译通过，烧写验证，没跑起来……
后来发现是上述创建定时器时的定时周期哪里使用了系统时钟的宏configCPU_CLOCK_HZ，想要使用configTICK_RATE_HZ滴答1S的宏，修改后正常运行：
![run_pass](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-16-run.png)  

17、定时器数量及定时器消息队列之间的关系
![depandent](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-17-timer-count-and-messageque-length.png)  

18、如果有其他任务，定时器没运行起来，则可能时定时器的优先级太低，被饿死了，需要将定时器的优先级相应的提高
![timer_priority](/img/frame/freertos/chapter5-timer/FRTOS-5-timer-18-timer-priority.png)  

19、定时器任务栈深度，需要根据实际定时器回调函数的使用情况进行设置，这里示例只是输出个调试信息，深度为1可以正常运行



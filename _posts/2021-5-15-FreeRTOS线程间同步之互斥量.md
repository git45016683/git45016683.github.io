---
layout:     post
title:      FreeRTOS线程间同步之互斥量
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-15
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 线程间同步之互斥量
#### 互斥量是一种特殊的信号量！！！
> 所以其句柄依然是xSemaphoreHandle类型

1、创建互斥量
![define_metux](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-1-create-mutex-1.png)  
![create_metux](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-1-create-mutex-2.png)  

2、使用互斥量
![demo_thead1](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-2-mutex-demo-1.png)  
![demo_thead2](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-2-mutex-demo-2.png)  

3、运行结果
![run](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-3-mutex-demo-run.png)  
可以看到后面都是任务2的信息在输出，这是因为任务2的优先级最高，它释放了互斥量后，马上又获取到，其他两个任务饿死了……
修改：增加一个切换时间片或者阻塞的状态到释放完互斥量后面
![demo_thead_change1](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-3-mutex-demo-change-1.png)  
![demo_thead_change2](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-3-mutex-demo-change-2.png)  

4、验证结果：可以看到任务二还是最常被系统调度运行的，因为其优先级高，而任务1/3则看系统调度，因为他俩优先级一致，按官方文档说法，优先级一致的情况下，会优先调度等待时间长的任务
![run_after_demo_thead_change](/img/frame/freertos/chapter3-thread-sync/mutex/FRTOS-3-mutex-4-mutex-demo-change-run.png)  



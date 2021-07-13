---
layout:     post
title:      FreeRTOS线程间通信之事件
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-21
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 线程间通信之事件
#### 事件依赖于动态申请内存，只要开启了该宏，事件相关的功能同时被开启，但如果要使用事件相关的宏/函数，还需要添加对应的头文件

1、添加头文件
![include_head]](/img\frame\freertos\chapter4-thread-communication\event\FRTOS-4-event-1-include-head.png)  

2、声明事件句柄和创建事件
![define](/img\frame\freertos\chapter4-thread-communication\event\FRTOS-4-event-2-define-event-1.png)  
![create](/img\frame\freertos\chapter4-thread-communication\event\FRTOS-4-event-2-create-event-2.png)  

3、创建事件示例的线程：  
线程1：阻塞3S，发送事件1（1<<0）；然后阻塞3S，发送事件4（1<<3）;然后阻塞3S，发送事件6（1<<5）；重复循环  
线程2：等待事件1或者事件4，超时时间为10S，读取事件后不清除  
线程3：等待事件1以及事件6，最大超时事件(阻塞式)  

这样一来，理论上线程2接收到任意事件都会唤醒执行，并且存在超时的可能；线程3则需要满足两个事件都触发才会唤醒执行  
![create_demo_thread1](/img\frame\freertos\chapter4-thread-communication\event\FRTOS-4-event-3-create-demo-threads-1.png)  
![create_demo_thread2](/img\frame\freertos\chapter4-thread-communication\event\FRTOS-4-event-3-create-demo-threads-2.png)  
![create_demo_thread3](/img\frame\freertos\chapter4-thread-communication\event\FRTOS-4-event-3-create-demo-threads-3.png)  

4、烧写验证
![run](/img\frame\freertos\chapter4-thread-communication\event\FRTOS-4-event-4-run.png)  

--------------------------TODO：只释放，不获取事件，上述的示例会导致系统卡死  



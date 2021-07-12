---
layout:     post
title:      RT-Thread线程间同步之事件集
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-01
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 线程间同步之事件集
##### 事件默认关闭，如果需要使用事件，则需要在rtconfig.h头文件中启用事件
> ![config](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-0-config-rt_using_event.png)  
事件是一种灵活的线程同步机制，每个线程由一个32位的无符号整型来表示一个事件集，一个事件集包含32个事件，由此可以实现事件与线程的一对多或者多对多。  
下面举个例子：  
①只有除数与被除数都准备OK后，进行除法运算(除数/被除数)  
②除数与被除数任一准备OK，则输出调试信息表明其已准备OK  


1、声明事件集及线程控制块指针(随机除数线程，随机被除数线程，除法运算线程)
![define](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-1-define-event-and-demo-threads.png)  

2、创建事件集及相关线程
![create](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-2-create-event-and-demo-threads.png)  

3、示例函数实现及解释
![demo_threads](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-3-thread-func.png)  

4、烧写验证
![build_run](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-4-build-run.png)  

------------------------↑动态创建----静态初始化↓-----------------------  


直接上代码，这里只是静态创建事件集和线程，其他跟上面动态创建的一样：
初始化事件集及线程
![init_event](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-5-init-event-and-demo-threads.png)  

线程中的事件集发送和接收：
![demo_threads_static](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-6-thread-func-static.png)  

执行验证结果：
![run_static](/img/frame/rt-thread/chapter3-thread-sync/event/RTT-3-event-7-build-run-static.png)  


---
layout:     post
title:      RT-Thread线程间通信之消息队列
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-03
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 线程间通信之消息队列
##### 消息队列默认关闭，如果需要启用，需要在rtconfig.h中开启
> ![config](/img/frame/rt-thread/chapter4-thread-communication/messageque/RTT-4-msgque-0-config-rt_using_messagequeue.png)  

1、声明、创建消息队列及示例线程
![create_msgque](/img/frame/rt-thread/chapter4-thread-communication/messageque/RTT-4-msgque-1-define-create-messagequeue-demothreads.png)  

2、发送及接收函数说明
![demo_thread](/img/frame/rt-thread/chapter4-thread-communication/messageque/RTT-4-msgque-2-thread-sendrecv-func.png)  

3、编译烧写验证
![run](/img/frame/rt-thread/chapter4-thread-communication/messageque/RTT-4-msgque-3-build-run.png)  

4、发送执行时长大于接收超时时限，则会超时
![timeout](/img/frame/rt-thread/chapter4-thread-communication/messageque/RTT-4-msgque-4-recv-timeout.png)  


------------------------↑动态创建----静态初始化↓-----------------------  

直接上代码，这里只是静态创建消息队列和线程，其他跟上面动态创建的一样：
![demo_static](/img/frame/rt-thread/chapter4-thread-communication/messageque/RTT-4-msgque-5-init-messagequeue-demothreads-static.png)  

烧写验证：
![run_static](/img/frame/rt-thread/chapter4-thread-communication/messageque/RTT-4-msgque-6-build-run-static.png)  


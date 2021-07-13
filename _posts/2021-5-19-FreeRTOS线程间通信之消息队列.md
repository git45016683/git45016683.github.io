---
layout:     post
title:      FreeRTOS线程间通信之消息队列
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-19
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 线程间通信之消息队列

1、创建队列API: xQueueCreate(queue.h)，其受控于动态申请的宏configSUPPORT_DYNAMIC_ALLOCATION(FreeRTOS.h)，并且该宏默认是启用的
![default_enable1](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-1-macro-enable-default-1.png)  
![default_enable2](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-1-macro-enable-default-2.png)  

2、声明并创建消息队列
![create_msgque](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-2-define-create-messageque.png)  

3、添加头文件
![include_head](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-3-include-head.png)  

4、发送函数  
xQueueSend：// 写入到队末，队列满后，超时不会插入  
xQueueSendToBack：// 写入到队末，队列满后，超时不会插入  

上面两个宏定义内函数调用其实是一样的：
![send](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-4-send-1.png)  
![sendtoback](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-4-sendtoback-2.png)  
xQueueOverwrite：// 写入到队末，队列满后，超时不会插入  
xQueueSendToFront：// 写入到队末，队列满后，超时不会插入  


5、5、接收函数  
xQueueReceive：//取出消息，并从队列中删除该消息  
xQueuePeek：//取出消息，不删除队列中的该消息  

6、xQueueSend发送时，队列满了，会如何？使用xQueueSend发送100ms一次
![send_1](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-6-send-1.png)  
6.1）采用xQueuePeek读取消息，每1S读一次
![recv_2](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-6-recv-2.png)  
![run_3](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-6-run-3.png)  
6.2）采用xQueueReceive读取消息，每1S读一次
![recv_4](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-6-recv-4.png)  
![run_5](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-6-run-5.png)  

7、xQueueSendToBack的实际实现跟xQueueSend完全一致，这里就不尝试了，队列满了，使用xQueueSendOverwrite会如何？使用xQueueSendOverwrite发送100ms一次
![7send_1](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-7-send-1.png)  
7.1）采用xQueuePeek读取消息，每1S读一次
![7recv2_run](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-7-recv-2-run.png)  
7.2）采用xQueueReceive读取消息，每1S读一次
![7recv3_run](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-7-recv-3-run.png)   

8、xQueueSendToFront发送时，队列满了，会如何？使用xQueueSendToFront发送100ms一次
![8send_1](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-8-send-1.png)  
8.1）采用xQueuePeek读取消息，每1S读一次
![8recv2_run](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-8-recv-2-run.png)  
8.2）采用xQueueReceive读取消息，每1S读一次
![8recv3_run](/img/frame/freertos/chapter4-thread-communication/messageque/FRTOS-4-messageque-8-recv-3-run.png)  


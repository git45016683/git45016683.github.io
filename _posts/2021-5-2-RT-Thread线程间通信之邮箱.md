---
layout:     post
title:      RT-Thread添线程间通信之邮箱
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-02
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 线程间通信之邮箱
##### 邮箱功能默认打开，如果需要关闭，需要在rtconfig.h头文件中注释掉/删掉
> ![config](/img/frame/rt-thread/chapter4-thread-communication/mailbox/RTT-4-mailbox-0-config-rt_using_mailbox.png)  

1、创建邮箱及相关线程
![create_mailbox](/img/frame/rt-thread/chapter4-thread-communication/mailbox/RTT-4-mailbox-1-define-create-mailbox-and-demothreads.png)  

2、接收发送邮件示例说明
![demo_thread](/img/frame/rt-thread/chapter4-thread-communication/mailbox/RTT-4-mailbox-2-thread-sendrecv-func.png)  

3、烧写验证
![run](/img/frame/rt-thread/chapter4-thread-communication/mailbox/RTT-4-mailbox-3-build-and-run.png)  
可以看到接收函数并没有延时函数，一直在while(1)无限循环执行，但实际输出却不会一直猛刷屏，而是有固定输出间隔。  
由此可以得出，邮箱接收是被动式触发的，有两种含义：  
>1、超时被动式-->如果在规定时间内没收到邮件，则跳出阻塞，重新循环进入下一轮等待接收  
2、接收到邮件被唤醒-->线程一直阻塞在等待，线程处于挂起状态，等待接收到邮件再唤醒进行处理  

WARNING: 没创建邮箱，触发邮箱接收时，不是提示邮箱不存在或者编译报错或者异常，而是会报邮箱满(-3).  


------------------------↑动态创建----静态初始化↓-----------------------  
直接上代码，这里只是静态创建邮箱控制块和线程，其他跟上面动态创建的一样：
![init_mailbox](/img/frame/rt-thread/chapter4-thread-communication/mailbox/RTT-4-mailbox-5-init-mailbox-and-demothreads-static.png)  
![demo_thread_static](/img/frame/rt-thread/chapter4-thread-communication/mailbox/RTT-4-mailbox-6-thread-sendrecv-func-static.png)  
![run_static](/img/frame/rt-thread/chapter4-thread-communication/mailbox/RTT-4-mailbox-7-build-and-run-static.png)  



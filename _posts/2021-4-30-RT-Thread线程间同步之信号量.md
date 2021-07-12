---
layout:     post
title:      RT-Thread线程间同步之信号量
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-04-30
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 线程间同步之信号量
##### 信号量默认开启，如果需要关闭，需要在rtconfig.h头文件将其对应的宏定义注释掉/删掉
> ![config](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-0-config-rt_using_semaphore.png)  

1、声明信号量
![define_semaphore](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-1-define_semaphore.png)  

2、创建信号量
![create_semaphore](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-2-create_semaphore.png)  

3、声明并创建线程(详解可查看RTT入门体验)
![create_demo_threads](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-3-create-demo-thread.png)  

4、释放信号量-finsh指令获取函数：收到非空字节即释放信号量
![release_semaphore](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-4-release-semaphore.png)  

5、捕捉信号量
![take_semaphore](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-4-release-semaphore.png)  
![add_header_path](/img/frame/rt-thread/chapter2-finsh-component/RTT-2-5-add-finsh-head-path-2.png)  

6、编译通过，烧写成功
![flash](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-6-build-pass.png)  

7、开启信号量线程验证
![run](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-7-run.png)  

------------------------↑动态创建----静态初始化↓-----------------------  


1、声明静态信号量
![define_semaphore_static](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-1-define_semaphore-static.png)  

2、初始化信号量
![init_semaphore](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-2-init_semaphore-static.png)  

3、创建一个线程用于静态信号量使用示例
![init_demo_thread](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-3-create-demo-thread-static.png)  

4、释放信号量
![release_semaph](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-4-take-semaphore-static.png)  

5、编译通过，烧写成功
![take_semaph](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-5-build-pass-static.png)  

6、验证
![run_static](/img/frame/rt-thread/chapter3-thread-sync/semaphore/RTT-3-sema-6-run-static.png)  

<!-- ![404](/img/frame/404-bg.jpg) -->

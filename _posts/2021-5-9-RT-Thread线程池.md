---
layout:     post
title:      RT-Thread线程池示例
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-09
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 线程池示例
##### Nano版配置文件没有线程池相关的选项可配置，默认关闭该功能，如果需要开启，需要添加
'RT_USING_MEMPOOL' 的宏定义



1、声明并创建内存池控制块
![define](/img/frame/rt-thread/chapter6-threadpool/RTT-6-threadpool-1-define-create-threadpool.png)  

2、声明并创建内存申请/释放线程，内存回收定时器
![create](/img/frame/rt-thread/chapter6-threadpool/RTT-6-threadpool-2-create-demo-threads.png)  

3、申请/释放内存线程说明
![demo_thread](/img/frame/rt-thread/chapter6-threadpool/RTT-6-threadpool-3-thread-func.png)  

------------------------↑动态创建----静态初始化↓-----------------------

4、直接上代码，这里只是静态创建互斥量和线程，其他跟上面动态创建的一样：
![demo_thread_static](/img/frame/rt-thread/chapter6-threadpool/RTT-6-threadpool-4-init-demo-threads-static.png)  

5、验证效果：
![run](/img/frame/rt-thread/chapter6-threadpool/RTT-6-threadpool-5-run.png)  
 



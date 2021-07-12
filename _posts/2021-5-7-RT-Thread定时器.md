---
layout:     post
title:      RT-Thread定时器示例
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-07
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 定时器示例

1、声明定时器控制块指针：1-周期性定时器，2-一次性定时器
![define](/img/frame/rt-thread/chapter5-timer/RTT-5-timer-1-define-timer.png)  

2、创建定时器
![create](/img/frame/rt-thread/chapter5-timer/RTT-5-timer-2-create-timer.png)  

3、实现定时器回调函数
![timer_callback](/img/frame/rt-thread/chapter5-timer/RTT-5-timer-3-timer-callback-func.png)  

4、编译烧写验证
![run](/img/frame/rt-thread/chapter5-timer/RTT-5-timer-4-build-run.png)  

------------------------↑动态创建----静态初始化↓-----------------------  

直接上代码，这里只是静态创建互斥量和线程，其他跟上面动态创建的一样：
![timer_static](/img/frame/rt-thread/chapter5-timer/RTT-5-timer-5-init-timer-static.png)  



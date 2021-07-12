---
layout:     post
title:      FreeRTOS线程间同步之临界区
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-15
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 线程间同步之临界区
#### 临界区是提供互斥功能的一种原始方式，可以简单粗暴的实现线程之间的互斥，确保线程间数据同步是稳定可信的
> 临界区有两种，一种是关闭中断及系统任务的  
taskENTER_CRITICAL();
taskEXIT_CRITICAL();  
另一种是关闭系统任务调度(禁止系统任务调度，直到重新开启任务调度)的  
vTaskSuspendScheduler();  / vTaskSuspendAll();
vTaskResumeScheduler(); / vTaskResumeAll();


1、未使用临界区的示例，例如我们理想先在任务1输出10次信息，再到任务2输出10次信息，最后到任务3输出10次信息，如此循环
![sample](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-1-sample-demo.png)  

2、实际运行情况如下：
![sample_run](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-2-sample-demo-run.png)  

3、增加临界区：
![add_critical](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-3-sample-demo-add-critical.png)  

4、运行效果，达成预期
![add_critical_run](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-4-sample-demo-add-critical-run.png)  

5、使用挂起调度器方式，这种方式中断是依然可以触发的
![suspend_1](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-5-sample-demo-suspend-1.png)  
![suspend_2](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-5-sample-demo-suspend-2.png)  

6、中断触发情况下，无阻塞，死循环输出信息，然后用串口中断触发输出一个信息
![output_debug_info_in_while1](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-6-sample-demo-suspend-run.png)  

7、运行效果：中断触发被正常执行
![run_ok](/img/frame/freertos/chapter3-thread-sync/critical/FRTOS-3-critical-7-sample-demo-suspend-run.png)  



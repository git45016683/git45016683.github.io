---
layout:     post
title:      FreeRTOS线程间通信之任务通知
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-22
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 线程间通信之任务通知
#### 任务通知
> 每个RTOS任务都有一个32位的通知值，任务创建时，这个值被初始化为0。RTOS任务通知相当于直接向任务发送一个事件，接收到通知的任务可以解除阻塞状态，前提是这个阻塞事件是因等待通知而引起的。发送通知的同时，也可以可选的改变接收任务的通知值。  
   可以通过下列方法向接收任务更新通知：  
	○ 不覆盖接收任务的通知值  
	○ 覆盖接收任务的通知值  
	○ 设置接收任务通知值的某些位  
	○ 增加接收任务的通知值  


1、查看任务控制块的通知定义，其定义在taskTCB任务控制块中，受控于宏configUSE_TASK_NOTIFICATIONS，该宏默认开启，所以任务通知功能默认开启
![default_disable](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-1-macro-disable-1.png)  
![enable](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-1-macro-enable-2.png)  

2、任务通知可以只发送通知，不附带任何值，这种用法，像不像信号量类型的用法？所以任务通知的接口也提供了类似信号量接口的形式，方便使用和区分  
xTaskNotifyGive：释放任务通知   【xSemaphoreGive-释放信号量】  
ulTaskNotifyTake：获取任务通知 【xSemaphoreTake-获取信号量】  
2.1、使用示例：task1每隔8S释放一个任务通知给task2，task2则每隔1S尝试去获取任务通知，获取超时时间为10S
![2release_notify_1](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-2-release-notify-1.png)  
![2take_notify_2](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-2-take-notify-2.png)  

2.2、烧写验证
![2run_3](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-2-run-3.png)  

3、既然任务通知可以不附带任何值，那么其也可以有附带值的用法：  
xTaskNotify：释放任务通知，附带更新的任务通知值，并且可以设定任务通知值的更新方式  
xTaskNotifyWait：获取任务通知  
3.1、使用示例：task3每隔6S释放一个任务通知给task4，通知值为1，并且以通知值累加的方式进行，task4每隔1S尝试获取任务通知，获取超时时间为8S；  
由于并没有清除通知值，且使用了通知值累加的方式，那么可以预期到，获取到的通知值会一直累加  
![3release_notify_1](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-3-release-notify-1.png)  
![3take_notify_2](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-3-take-notify-2.png)  
printf("/r/nNotifyTask4: %d, ret %ld, notifyValue=%d", task2TestCount++, ret, comeIMG);  
3.2、烧写验证
![3run_3](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-3-run-3.png)  

4、任务通知还提供一个接口，释放通知的时候，可以获取到前一个未被获取的通知值。可以根据这个参数以及函数的返回值，判断前一个通知值是什么以及是否被更新  
xTaskNotifyAndQuery：如果通知值更新方式被设置为eSetValueWithoutOverwrite，不能被覆写，如果前一次释放的任务通知值未被获取，就再次触发了任务通知的释放，那么其会返回pdFALSE，通知值更新失败。此时可以根据最后一个参数，查询前一个通知值是什么  
4.1、使用示例，task5每隔2S释放一次任务通知，通知值以非覆写的方式进行，通知值为5、6来回变化；task6每隔5S获取一次任务通知，获取超时时间同样为5S，那么预期的结果会是task5在成功释放一次任务通知后，会出现一次释放任务通知失败，第一次失败的通知值应该为6  
![4release_notify_1](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-4-release-notify-1.png)  
![4take_notify_2](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-4-take-notify-2.png)  

4.2、烧写验证
![4_run3](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-4-run-3.png)  

4.3、使用这个接口，如果任务通知值的更新方式没有设置为非覆写的情况下，则不会起到上述的效果：
![config_overwrite](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-4-config-overwrite-type-4.png)  

4.4、验证：
![4run_5](/img/frame/freertos/chapter4-thread-communication/notify/FRTOS-4-notify-4-run-5.png)  



---
layout:     post
title:      FreeRTOS线程间通信之信号量
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-18
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 线程间通信之信号量

1、定义信号量句柄
![define](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-1-semaphore-define.png)  

2、xSemaphoreHandle找不到定义，所以报红X，加入头文件，不再报错
![not_found_head](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-2-add-include-semaphore-1.png)  
![add_head](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-2-add-include-semaphore-2.png)  

3、创建二值信号量
![create_binaray_semaphore](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-3-create-binary-semaphore.png)  

4、创建释放信号量的线程
![release_semaphore_thread](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-4-release-semaphore.png)  

5、创建接收信号量的线程
![take_semaphore_thread](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-5-take-semaphore.png)  

6、那么应该会有两次获取不到信号量，超时返回pdFALSH，烧写验证
![run](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-6-run.png)  

7、中断版本也是这两个函数，后缀多了FromISR  
xSemaphoreGiveFromISR(semaphHandle, &pxHigherPriorityTaskWoken);  
xSemaphoreTakeFromISR(semaphHandle, &pxHigherPriorityTaskWoken);  
// 第二个参数是表明接收线程是否需要优先执行(线程优先级反转)  

如果在二值信号量正在处理中，又有中断触发了二值信号量，然后又有一次中断触发，那么可能会导致这俩次中断触发的信号量被丢失，不能正确进入处理，因为二值信号量只能keep住空非空的状态
模拟试试：  
![sendmore](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-7-sendmore-1.png)  
接收信号量的地方跟上面一样，不变，烧写验证下：
![recv_same_run_again](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-7-recv-same-run-2.png)  
跟之前一样，只能一次信号量。那么可以适用计数信号量来处理这种情况

-----------------------计数信号量----------------------------------  

8、创建计数信号量
![create_counting_semaphore](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-8-create-count-semaphore.png)  

9、创建计数信号量释放线程，每隔11S连续释放6个计数信号量
![create_demo_send_thread](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-9-create-send-count-sema-thread.png)  

10、创建计数信号量获取线程，每0.8S获取一次
![create_demo_recv_thread](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-10-create-recv-count-sema-thread.png)  

11、那么理论上获取应该可以将所有的信号量都成功获取并处理，编译烧写验证之
编译出错，未定义的函数
![build_error](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-11-build-error-1.png)  
configSUPPORT_DYNAMIC_ALLOCATION宏默认开启，那么就是configUSE_COUNTING_SEMAPHORES这个宏需要开启一下  
![macro_disable](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-11-macro-disable-2.png)  
在FreeRTOSConfig.h头文件中启用之  
![macro_enable](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-11-macro-enable-3.png)  

12、编译通过，烧写验证：连续的信号量被成功获取
![run_again](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-12-count-semaphore-run.png)  
由上面计数信号量及二值信号量的创建、释放、获取来看，只有创建是不一样的，获取及释放的用法完全一致。

其中断服务程序版本也是完全一致，根据需要选择合适的信号量进行使用即可。



PS：不可以在线程中创建二值信号量，会导致前后不一
![not_creat_binary_sema_in_thread](/img/frame/freertos/chapter4-thread-communication/semaphore/FRTOS-4-semaphore-13-can-not-create-binary-sema-in-thread.png)  

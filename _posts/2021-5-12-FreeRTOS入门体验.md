---
layout:     post
title:      FreeRTOS入门体验
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-12
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 入门体验

1、新建存放rtt文件的文件夹，这里命名为rtos
![rtos_folder](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-1-folder.png)  

2、将从freertos的github上下载下来的文件，拷贝source到rtos路径下
![copy_files](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-2-copy-freertos-files.png)  
>include: 头文件目录  
portable: 硬件接口相关文件夹(芯片接口相关/内存管理相关等)  
	- Keil: ARM-MDK IDE的启动文件(硬件接口)相关-->里面就一个文件说明跟RVDS一样，所以后面移植会直接移植RVDS的内容  
	- MemMang: 内存管理相关  
	- RVDS:   
	- GCC: GCC编译环境的启动文件相关  
	- ….  

*.c：freertos的列表队列任务等实现源文件  

3、将上述Source文件夹拷贝到rtos文件夹内，并将其添加到git版本管理
![add_to_git](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-3-add-to-git.png)  

4、将非必要的IDE相关的启动文件删掉，只保留我们需要用到的keil相关的文件，精简工程rt_hw_console_getchar(void)
![delete_not_needly_files](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-4-delete-needless-files.png)  

5、将freertos内核相关的文件修改为只读属性，避免误修改【非必要】
![read_only](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-5-set-readonly.png)  

6、添加freertos的文件到MDK工程，点击OK
![add_freertos_files_to_mdk1](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-6-add-freertosfiles-to-mdk-1.png)  
![add_freertos_files_to_mdk2](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-6-add-freertosfiles-to-mdk-2.png)  

7、从demo中找到相近的工程，将其里面的FreeRTOSConfig.h头文件拷贝到MDK工程目录下
![copy_config_files](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-7-copy-freertosconfig-to-mdk.png)  
后面配置freertos的功能需要在这个配置文件进行

8、将freeRTOSConfig.h头文件添加到MDK工程
![add_config_to_mdk](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-8-add-freertosconfig-to-mdk.png)  

9、将freeRTOS的头文件路径添加到MDK工程
![add_headfile_path](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-9-add-headfile-path-to-mdk.png)  

10、将PendSV_Handler/SVC_Handler两个中断服务函数修改定义向FreeRTOS提供的函数xPortPendSVHandler/vPortSVCHandler  
有两种方法：  
1）修改启动文件内的两个中断服务函数名为xPortPendSVHandler/vPortSVCHandler
![change_ithadler_1](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-10-change-ithandler-1.png)  
2）将xPortPendSVHandler/vPortSVCHandler重定义为PendSV_Handler/SVC_Handler  
 - 如果使用2方式，并且使用CubeMx生成的工程，那么需要在stm32f10x_it.c文件内将PendSV_Handler/SVC_Handler两个中断函数注释掉，不然会报多重定义的错误  
这里示例使用第2种方式：  
![change_ithadler_2](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-10-change-ithandler-2.png)  
主要是函数声明类型跟INIT_xxx_EXPORT要求的不一样，将函数返回值由void修改为int即可，当然函数内亦需要按实际情况return一个值。  

11、将FreeRTOS的时钟中断服务函数xPortSysTickHandler添加到SysTick_Handler函数中，参考[链接](https://bbs.21ic.com/icview-2897082-1-1.html)
![config_systick_1](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-11-config-systickhandler-1.png)  
![config_systick_2](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-11-config-systickhandler-2.png)  

12、将FreeRTOS.h/task.h头文件添加到main.h
![include_head](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-12-include-task-head.png)  

13、根据[API文档](https://www.freertos.org/xTaskCreateStatic.html)或者在task.h文件中找到静态创建线程(任务)的接口，创建一个任务
![init_thread_1](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-13-init-thread-static-1.png)  
![init_thread_2](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-13-init-thread-static-2.png)  

14、编译提示错误
![build_error](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-14-build-error.png)  
查看xTaskCreateStatic函数的定义，发现函数被一个宏预编译包裹着configSUPPORT_STATIC_ALLOCATION
![func_define](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-14-func-define-static.png)  
查看这个宏的定义，发现默认关闭
![disable_macro](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-14-func-define-macro-disable-static.png)  

15、知道这个宏configSUPPORT_STATIC_ALLOCATION默认关闭后，重新将该宏在FreeRTOSConfig.h头文件开启
![enable_macro](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-15-enable-macro-static.png)  

16、再次编译发现一个函数vApplicationGetIdleTaskMemory没有定义
![build_error_again](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-16-build-error.png)  
查看一下该函数，只有个extern声明
![only_define](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-16-only-define.png)  
尝试将该函数添加空实现，编译通过了
![add_null_func](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-16-add-func.png)  
研究一下该函数的作用：通过该函数来给空闲任务的任务堆 栈及任务控制块分配内存，添加对应的实现
![add_func_detail](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-16-func.png)  

17、在任务内添加调试信息输出作验证
![add_debug_info](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-17-add-debug-info.png)  
编译烧写后，每秒输出了一次调试信息
![build_run](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-17-build-run.png)  

18、新建一个mythread.c，用来声明和实现动态/静态创建任务
![add_mythread_file](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-18-add-mythread-file.png)  

19、在AppTasksCreate任务内创建动态线程
![create_thread](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-19-create-thread-dynamic.png)  

20、编译烧写验证，运行正常
![run](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-20-run-ok.png)  

21、额外创建多一个静态线程，动态静态一起创建任务，多任务跑起来
![init_thread_static_1](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-21-init-thread-static-1.png)  
![init_thread_static_2](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-21-init-thread-static-2.png)  

编译烧写运行：
![build_and_run](/img/frame/freertos/chapter1-try-to-experience/FRTOS-1-21-build-run-3.png)  


<!-- ![404](/img/frame/404-bg.jpg) -->

---
layout:     post
title:      FreeRTOS添加命令行实现
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-05-13
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# FreeRTOS 添加命令行实现
##### FreeRTOS没有像RT-Thread一样内置集成了Finsh一样的现成命令行组件，但是官方提供了CLI(Command Line Interface)的demo以及相关API，可以让需要者参考实现
> V9.0版本下，demo路径为：FreeRTOS/FreeRTOS-Plus/Demo/Common/FreeRTOS_Plus_CLI_Demos


1、基于stm32f103系列做移植，我们使用串口，所以将下面两个文件拷贝到我们的路径下
![copy_demo_files](/img/frame/freertos/chapter2-command-line/FRTOS-2-1-copy-clifile-from-demo.png)  

2、打开UARTCommandConsole文件，查看其依赖关系，发现有FreeRTOS_CLI.h/serial.h两个头文件挺陌生
![check_head_depandend](/img/frame/freertos/chapter2-command-line/FRTOS-2-2-found-other-files.png)  
>include: 头文件目录
portable: 硬件接口相关文件夹(芯片接口相关/内存管理相关等)
	- Keil: ARM-MDK IDE的启动文件(硬件接口)相关-->里面就一个文件说明跟RVDS一样，所以后面移植会直接移植RVDS的内容
	- MemMang: 内存管理相关
	- RVDS: 
	- GCC: GCC编译环境的启动文件相关
	- ….

*.c：freertos的列表队列任务等实现源文件

3、找到serial相关文件(.c/.h)并拷贝到工程目录下[serial.h在FreeRTOS/Demo/Common/include下；serial.c可根据实际板子及编译器情况，可以在对应(或相近)的demo路径下找到，例如这里使用stm32f10x系列，serial.c可以在FreeRTOS/Demo/CORTEX_STM32F103_Keil/serial下找到]
![copy_files_to_mdk_1](/img/frame/freertos/chapter2-command-line/FRTOS-2-3-copy-clifile-from-demo-1.png)  
![copy_files_to_mdk_2](/img/frame/freertos/chapter2-command-line/FRTOS-2-3-copy-clifile-from-demo-2.png)  
![copy_files_to_mdk_3](/img/frame/freertos/chapter2-command-line/FRTOS-2-3-copy-clifile-from-demo-3.png)  
![copy_files_to_mdk_4](/img/frame/freertos/chapter2-command-line/FRTOS-2-3-copy-clifile-from-demo-4.png)  

4、找到FreeRTOS_CLI相关文件(.c/.h)并拷贝到工程目录下[FreeRTOS/FreeRTOS-Plus/Source/FreeRTOS-Plus-CLI]
![copy_cli_files](/img/frame/freertos/chapter2-command-line/FRTOS-2-4-copy-clifile-from-demo-2.png)  

5、Sample-CLI-command.c的头文件依赖如下，上面添加的依赖已经足够
![check_head_files](/img/frame/freertos/chapter2-command-line/FRTOS-2-5-confirm-include.png)  

6、在MDK工程为CLI添加一个独立的Groups并将上述文件添加到Groups，并添加头文件路径
![add_cli_group](/img/frame/freertos/chapter2-command-line/FRTOS-2-6-add-file-to-mdk.png)  
![add_head_path](/img/frame/freertos/chapter2-command-line/FRTOS-2-6-add-head-path.png)  

7、编译，报错
![build_error](/img/frame/freertos/chapter2-command-line/FRTOS-2-7-build-error.png)  

8、在FreeRTOS_CLI.h头文件中添加宏定义configCOMMAND_INT_MAX_OUTPUT_SIZE【也可以统一在FreeRTOSConfig.h头文件中定义】
![config_macro](/img/frame/freertos/chapter2-command-line/FRTOS-2-8-config-macro-in-cli.png)  

9、serial.c内串口相关的函数和宏定义都是库函数版本，改造成HAL库版本，并提供两个版本（中断接收及空闲中断+DMA接收）
  9.1) 添加头文件及串口声明
![add_headfile_and_uart_define](/img/frame/freertos/chapter2-command-line/FRTOS-2-9-1.png)  

9.2) xSerialPortInitMinimal修改：删掉函数开始的结构体定义，把if( ( xRxedChars != serINVALID_QUEUE ) && ( xCharsForTx != serINVALID_QUEUE ) ){}内的内容修改为
```cpp
HAL_UART_Receive_DMA(&huart2, (uint8_t*)uart2_recv_buff, sizeof(uart2_recv_buff));  // 使用DMA
```
或者
```cpp
HAL_UART_Receive_IT(&huart1, &aRxBuffer, 1);  // 不使用DMA，使用中断接收
![it_recv_without_dma](/img/frame/freertos/chapter2-command-line/FRTOS-2-9-2.png)  
```
9.3) xSerialPutChar修改：把USART_ITConfig( USART1, USART_IT_TXE, ENABLE );删掉，在同样的位置增加如下代码：
```cpp
uint8_t cChar;
if (xQueueReceive(xCharsForTx, &cChar, 0) == pdTRUE)
{
	while(HAL_UART_Transmit_IT(&huart2, &cChar, 1) != HAL_OK);
}
```
![change_xserialputchar](/img/frame/freertos/chapter2-command-line/FRTOS-2-9-3.png)  
主要是函数声明类型跟INIT_xxx_EXPORT要求的不一样，将函数返回值由void修改为int即可，当然函数内亦需要按实际情况return一个值。

9.4) 删掉(注释掉)中断服务函数vUARTInterruptHandler，将空闲中断接收一整帧数据的函数添加进来
```cpp
uint8_t buff_index = 0;
uint8_t data_len = 0;
void UART2_IDLECallback(UART_HandleTypeDef* huart)
{
	HAL_UART_DMAStop(&huart2);  // 停止本次DMA传输
	
	data_len = RECV_BUFF_MAX - __HAL_DMA_GET_COUNTER(&hdma_usart2_rx);  // 计算接收到的数据长度
	
	uart2_recv_buff[data_len] = '/0';
	
	portBASE_TYPE xHigherPriorityTaskWoken = pdFALSE;
	for (uint8_t i = 0; i < data_len; i++)
	{
		xQueueSendFromISR(xRxedChars, &uart2_recv_buff[i], &xHigherPriorityTaskWoken);
		xHigherPriorityTaskWoken = pdFALSE;
	}
	
	HAL_UART_Receive_DMA(&huart2, (uint8_t*)uart2_recv_buff, sizeof(uart2_recv_buff));  // 重启开始DMA传输 每次255字节数据
	portYIELD_FROM_ISR(xHigherPriorityTaskWoken);
}
```
![it_recv_with_dma](/img/frame/freertos/chapter2-command-line/FRTOS-2-9-4.png)  

10、编译，已经没有报serial相关的错误，而是报了两个未定义错误
![build_error_func_not_define](/img/frame/freertos/chapter2-command-line/FRTOS-2-10-build-error.png)  

11、找到vTaskList函数定义，发现受控于两个宏定义，在FreeRTOSConfig.h加上
![macro_disable](/img/frame/freertos/chapter2-command-line/FRTOS-2-11-macro-disable-1.png)  
![macro_define](/img/frame/freertos/chapter2-command-line/FRTOS-2-11-macro-define-2.png)  

12、全工程搜索找到xQueueCreateMutex的定义，发现受控于两个宏，其中configSUPPORT_DYNAMIC_ALLOCATION默认已开启，在FreeRTOSConfig.h添加上configUSE_MUTEXES的宏定义
![macro_disable1](/img/frame/freertos/chapter2-command-line/FRTOS-2-12-macro-disable-1.png)  
![macro_difine1](/img/frame/freertos/chapter2-command-line/FRTOS-2-12-macro-define-2.png)  

13、在main.h头文件增加相关宏定义
![enable_macro](/img/frame/freertos/chapter2-command-line/FRTOS-2-13-macro-define.png)  

14、在main.c中注册命令行命令
![regeister](/img/frame/freertos/chapter2-command-line/FRTOS-2-14-call-register-cli.png)  

15、引入注册命令行函数和创建命令控制台函数，创建命令控制台任务
![create_cli](/img/frame/freertos/chapter2-command-line/FRTOS-2-15-create-cli-thread.png)  

16、编译通过
![build_pass](/img/frame/freertos/chapter2-command-line/FRTOS-2-16-build-pass.png)  

17、烧写尝试命令行
![run](/img/frame/freertos/chapter2-command-line/FRTOS-2-17-run.png)  
按照上面的方式添加完成后，如果出现串口不停在打印数据，那么可以尝试降低串口波特率，参考[链接](https://www.programmersought.com/article/84803197029/)，基于stm32f103c8系列适用


----------------下面尝试自定义命令---------------------  

18、找到task-stats命令的定义，查看其定义及实现
![task_stats](/img/frame/freertos/chapter2-command-line/FRTOS-2-18-check-task-status-define.png)  

19、查看一下其结构体定义
![check_task_struct](/img/frame/freertos/chapter2-command-line/FRTOS-2-19-check-struct-define.png)  

20、拷贝一份task-stats的定义，修改为准备自定义的命令
![copy_cli_define_for_custom](/img/frame/freertos/chapter2-command-line/FRTOS-2-20-copy-cli-define-for-change.png)  

21、添加命令响应的回调函数prvTaskCreateStatic
![add_callback](/img/frame/freertos/chapter2-command-line/FRTOS-2-21-add-callback.png)  

22、在命令行注册函数中，注册自定义命令
![register_costom_cli](/img/frame/freertos/chapter2-command-line/FRTOS-2-22-register-mycli.png)  

23、将main.c里面静态创建任务TaskCreateStatic的调用注释，并将其声明的头文件加入到Sample-CLI-commands.c
![23_1](/img/frame/freertos/chapter2-command-line/FRTOS-2-23-1.png)  
![23_2](/img/frame/freertos/chapter2-command-line/FRTOS-2-23-2.png)  

24、编译烧写验证
![build_and_run](/img/frame/freertos/chapter2-command-line/FRTOS-2-24-build-run.png)  

25、成功添加到自定义命令，而且由上述的情况可知，输入的命令要跟命令帮助说明中的：前面的一致才会执行（当然，CLI命令结构体中明确说明两者要一致）
![run_comstom-cli](/img/frame/freertos/chapter2-command-line/FRTOS-2-25.png)  

----------------下面尝试启用自带的一些未启用的命令---------------------  

26、尝试启用任务运行时间的命令
![try_get_task_run_time](/img/frame/freertos/chapter2-command-line/FRTOS-2-26.png)  

27、在FreeRTOSConfig.h中启用该宏
![macro_enable](/img/frame/freertos/chapter2-command-line/FRTOS-2-27.png)  

28、编译出错，如果configGENERATE_RUN_TIME_STATS宏定义启用，那么portCONFIGURE_TIMER_FOR_RUN_TIME_STATS这个宏也必须定义，并且该宏必须要调用一个定时器或者计数器的函数作为运行时间的基准
![portCONFIGURE_TIMER_FOR_RUN_TIME_STATS](/img/frame/freertos/chapter2-command-line/FRTOS-2-28.png)  

29、添加宏定义portCONFIGURE_TIMER_FOR_RUN_TIME_STATS，并将系统获取tick计数的函数赋值
![portCONFIGURE_TIMER_FOR_RUN_TIME_STATS_enable](/img/frame/freertos/chapter2-command-line/FRTOS-2-29.png)  

30、再编译，还有宏没定义portGET_RUN_TIME_COUNTER_VALUE
![portGET_RUN_TIME_COUNTER_VALUE](/img/frame/freertos/chapter2-command-line/FRTOS-2-30.png)  

31、再次定义portGET_RUN_TIME_COUNTER_VALUE宏，并且该宏由定义看起来就像获取tick值的，将系统获取tick计数的函数赋值
![portGET_RUN_TIME_COUNTER_VALUE_enable](/img/frame/freertos/chapter2-command-line/FRTOS-2-31.png)  

32、编译通过啦，烧写验证
![build_pass_and_run](/img/frame/freertos/chapter2-command-line/FRTOS-2-32.png)  




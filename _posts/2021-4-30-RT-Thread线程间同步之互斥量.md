---
layout:     post
title:      RT-Thread线程间同步之互斥量
subtitle:   万事开头难, 更难在坚持[新坑体验]
date:       2021-04-30
author:     (icemaple)
header-img: img/frame/the-first.png
catalog:   true
tags:
    - 技术什锦
---
# RT-Thread Nano 线程间同步之互斥量
##### 访问同一数据，没有使用线程同步的情况下，可能会导致一些数据的变化不是我们所期望的，如下举个例子：
>  // 未使用互斥量进行线程间同步的线程:  
```cpp
rt_thread_t sampleThread1;
rt_thread_t sampleThread2;
int SampleTaskCreate(void)
{
	sampleThread1 = rt_thread_create("sampleThread1",
                             AddThread1_entery,
                             RT_NULL,
                             256,
                             4,
                             10);
	if (sampleThread1 != RT_NULL)
	{
		rt_thread_startup(sampleThread1);
	}
	else
	{
		return 0;
	}
	
	sampleThread2 = rt_thread_create("sampleThread2",
							AddThread2_entery,
							RT_NULL,
							256,
							2,
							10);
	if (sampleThread2 != RT_NULL)
	{
		rt_thread_startup(sampleThread2);
		return 1;
	}
	return 0;
}
INIT_APP_EXPORT(SampleTaskCreate);

static uint8_t number1, number2 = 0;
void AddThread1_entery(void* parameter)
{
	while(1)
	{
		number1++;
		rt_thread_mdelay(100);
		number2++;
	}
}

void AddThread2_entery(void* parameter)
{
	while(1)
	{
		if (number1 != number2)
		{
			rt_kprintf("\r\nnumer1(%d) != number2(%d)", number1, number2);
		} 
		else
		{
			rt_kprintf("\r\nnumer1 == number2, is %d", number1);
		}
		number1++;
		number2++;
		rt_thread_mdelay(1000);
	}
}
```

> 其运行结果如下：
numer1 == number2, is 0msh >
numer1(11) != number2(10)
numer1(22) != number2(21)
numer1(33) != number2(32)
numer1(44) != number2(43)
numer1(55) != number2(54)
numer1(66) != number2(65)
numer1(77) != number2(76)
numer1(88) != number2(87)
numer1(99) != number2(98)
numer1(110) != number2(109)
numer1(121) != number2(120)
numer1(132) != number2(131)
numer1(143) != number2(142)
numer1(154) != number2(153)
numer1(165) != number2(164)
numer1(176) != number2(175)
numer1(187) != number2(186)
numer1(198) != number2(197)
numer1(209) != number2(208)
numer1(220) != number2(219)
numer1(231) != number2(230)
numer1(242) != number2(241)
numer1(253) != number2(252)
numer1(8) != number2(7)
numer1(19) != number2(18)
numer1(30) != number2(29)
numer1(41) != number2(40)
numer1(52) != number2(51)
numer1(63) != number2(62)
numer1(74) != number2(73)

可以看到第一次进入thread2时，两个值是相等的，但是后面就不相等了，因为thread1中两个变量的自增中间有明显的延时，导致number1自增完后，线程1挂起，运行线程2，此时number2尚未自增，所以number2就一直都比number1小


#### 启用互斥量进行同步
1、互斥量默认关闭，如果需要使用互斥量，则需要在rtconfig.h头文件开启对应的宏定义
![confit](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-1-config-rt_using_mutex.png)  

2、声明并创建互斥量
![define_create](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-2-define-create-mutex.png)  

3、动态创建两个跟上述基本一样的线程，只是多了互斥量的引用
![demo_thread1](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-3-create-demo-thread-1.png)  
![demo_thread2](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-3-create-demo-thread-2.png)  

4、编译通过，烧写
![rt_hw_console_getchar](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-4-build-pass.png)  

5、验证：两个变量一直保持同步自增
![run](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-5-run.png)  

------------------------↑动态创建----静态初始化↓-----------------------  

直接上代码，这里只是静态创建互斥量和线程，其他跟上面动态创建的一样：
![static](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-6-mutex-static-demo-code.png)  

编译烧写验证结果：两个变量一直保持同步自增
![run_static](/img\frame\rt-thread\chapter3-thread-sync\mutex\RTT-3-mutex-6-mutex-static-run.png)  

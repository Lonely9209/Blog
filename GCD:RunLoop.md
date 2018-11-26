---
title: GCD/RunLoop
date: 2016-07-05 10:31:23
tags:
categories: 网络与多线程
---


## 一、GCD
#### 1、GCD简介
###### 什么是GCD
* 全称是Grand Central Dispatch
* 纯C语言，提供了非常多强大的函数

###### GCD的优势
* GCD是苹果公司为多核的并行运算提出的解决方案
* GCD会自动利用更多的CPU内核（比如双核、四核）
* GCD会自动管理线程的生命周期（创建线程、调度任务、销毁线程）
* 程序员只需要告诉GCD想要执行什么任务，不需要编写任何线程管理代码

#### 2、任务和队列
###### GCD中有2个核心概念
* 任务：执行什么操作
* 队列：用来存放任务

###### GCD的使用就2个步骤
* 定制任务:确定想做的事情
* 将任务添加到队列中:指定运行方式
	* GCD会自动将队列中的任务取出，放到对应的线程中执行
	* 任务的取出遵循队列的FIFO原则：先进先出，后进后出
	
###### 队列的类型
* 并发队列（Concurrent Dispatch Queue）
	
		可以让多个任务并发（同时）执行（自动开启多个线程同时执行任务）
并发功能只有在异步（dispatch_async）函数下才有
* 串行队列（Serial Dispatch Queue）

		让任务一个接着一个地执行（一个任务执行完毕后，再执行下一个任务

* 主队列

		特殊的串行队列，代表主线程

###### 执行任务
**GCD中有2个用来执行任务的函数**

* 同步的方式执行任务

		dispatch_sync(dispatch_queue_t queue, dispatch_block_t block);
* 异步的方式执行任务
	```objc
	dispatch_async(dispatch_queue_t queue, dispatch_block_t block);
	```
#### 3、容易混淆的术语
**有4个术语比较容易混淆：同步、异步、并发、串行**

###### 同步和异步决定了要不要开启新的线程
* 同步：在当前线程中执行任务，不具备开启新线程的能力
* 异步：在新的线程中执行任务，具备开启新线程的能力

###### 并发和串行决定了任务的执行方式
* 并发：多个任务并发（同时）执行
* 串行：一个任务执行完毕后，再执行下一个任务

#### 4、串行队列
* 以先进先出的方式，顺序调度队列中的任务执行
* 无论队列中所指定的执行任务函数是同步还是异步，都会等待前一个任务执行完成后，再调度后面的任务

###### 串行队列的执行
```objc
串行队列，同步执行
不开线程，同步执行(在当前线程执行)
#define DISPATCH_QUEUE_SERIAL NULL
dispatch_queue_t q = dispatch_queue_create("test", DISPATCH_QUEUE_SERIAL);
for (int i = 0; i < 10; i++) {
    //同步执行
    dispatch_sync(q, ^{
       NSLog(@"%@ -- %d",[NSThread currentThread],i);
    });
}

串行队列，异步执行
开一个线程，顺序执行
只有一个线程，因为是串行队列，只有一个线程就可以按顺序执行队列中的所有任务
dispatch_queue_t q = dispatch_queue_create("test", DISPATCH_QUEUE_SERIAL);
    for (int i = 0; i < 10; i++) {
        //异步执行
        dispatch_async(q, ^{
            NSLog(@"%@ -- %d",[NSThread currentThread],i);
        });
    }
```

#### 5、并发(并行)队列
* 以先进先出的方式，并发调度队列中的任务执行
* 如果当前调度的任务是同步执行的，会等待任务执行完成后，再调度后续的任务
* 如果当前调度的任务是异步执行的，同时底层线程池有可用的线程资源，会再新的线程调度后续任务的执行

###### 并行队列的执行
```objc
并行队列，异步执行
开多个线程，异步执行
开多个线程，异步执行，每次开启多少个线程是不固定的(线程数，不由我们控制)，线程数是由gcd来决定的
dispatch_queue_t q = dispatch_queue_create("test", DISPATCH_QUEUE_CONCURRENT);
    for (int i = 0; i < 10; i++) {
        //异步执行
        dispatch_async(q, ^{
            NSLog(@"%@ -- %d",[NSThread currentThread],i);
        });
    }

并行队列，同步执行
不开线程，顺序执行 ,与串行队列同步执行一样
```

#### 6、主队列
```objc
主队列，异步任务
不开线程，同步执行
主队列特点：如果主线程正在执行代码暂时不调度任务，等主线程执行结束后在执行任务
主队列又叫 全局串行队列

主队列，同步执行
程序执行不出来（死锁）
主队列：如果主线程正在执行代码，就不调度任务
同步执行：如果第一个任务没有执行，就继续等待第一个任务执行完成，再执行下一个任务此时互相等待，程序无法往下执行（死锁）
dispatch_sync(q, ^{
            NSLog(@"%@ -- %d",[NSThread currentThread],i);
        });
```
**注：主队列同步执行死锁**

#### 7、线程间通信示例
###### 从子线程回到主线程
```objc
dispatch_async(
dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // 执行耗时的异步操作...
      dispatch_async(dispatch_get_main_queue(), ^{
        // 回到主线程，执行UI刷新操作
        });
});
```

#### 8、延时执行
**iOS常见的延时执行有2种方式**

###### 调用NSObject的方法
```objc
[self performSelector:@selector(run) withObject:nil afterDelay:2.0];
```
###### 使用GCD函数
```objc
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
    // 2秒后异步执行这里的代码...
	});
```

#### 9、队列组
```objc
有这么1种需求
首先：分别异步执行2个耗时的操作
其次：等2个异步操作都执行完毕后，再回到主线程执行操作

如果想要快速高效地实现上述需求，可以考虑用队列组
dispatch_group_t group =  dispatch_group_create();
dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // 执行1个耗时的异步操作
});
dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // 执行1个耗时的异步操作
});
dispatch_group_notify(group, dispatch_get_main_queue(), ^{
    // 等前面的异步操作都执行完毕后，回到主线程...
});
```

#### 10、单例模式
###### 单例模式的作用
* 可以保证在程序运行过程，一个类只有一个实例，而且该实例易于供外界访问
* 从而方便地控制了实例个数，并节约系统资源
###### 单例模式的使用场合
* 在整个应用程序中，共享一份资源（这份资源只需要创建初始化1次）
	```objc
	static dispatch_once_t onceToken; 
	dispatch_once(&onceToken, ^{
	//要初始化的代码
	 }); 
	```
## 二、RunLoop
#### 1、什么是RunLoop?
* RunLoop就是消息循环,每一个线程内部都有一个消息循环。
* 只有主线程的消息循环默认开启,子线程的消息循环默认不开启。

#### 2、RunLoop的目的
* hn保证程序不退出 。
* 负责处理输入事件。
* 如果没有事件发生,会让程序进入休眠状态  。

#### 3、如何使用
###### 添加消息到循环中
* （1）创建输入源。(以NSTimer为例)
* （2）指定该事件（源）在循环中运行的模式，并加入循环。
	```objc
	--- [self performSelector:@selector(demo2) onThread:thread withObject:nil waitUntilDone:NO];
	---[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];
	```
###### 使用总结
1. 创建消息
2. 把消息放入循环，并指定消息运行的模式
3. 在与循环的模式匹配的时候，消息运行

#### 4、循环常用模式
* NSDefaultRunLoopMode--默认模式
* NSRunLoopCommonModes--普通模式

#### 5、子线程中的消息循环
* 特点：子线程默认不开启消息循环,主线程默认开启消息循环。
* 手动开启注意 
	```objc
	执行完[[NSRunLoop currentRunLoop] run];后面的代码不会执行,也关不了循环。
	可用[[NSRunLoop currentRunLoop] runUntilDate:[NSDate dateWithTimeIntervalSinceNow:5]];
	```
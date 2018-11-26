---
title: NSOperation
date: 2016-07-06 10:31:23
tags:
categories: 网络与多线程
---

## 一、NSOperation简介
#### 1、NSOperation的作用
* 是OC语言中基于GCD的面向对象的封装
* 使用起来比GCD更加简单（面向对象）
* 提供了一些用GCD不好实现的功能
* 苹果推荐使用，使用NSOperation不用关心线程以及线程的生命周期

#### 2、查看NSOperation的头文件
* NSOperation是一个抽象类
	1. 不能直接使用（方法没有实现）
	2. 约束子类都具有共同的属性和方法
* NSOperation的子类
	1. NSInvocationOperation
	2. NSBlockOperation
	3. 自定义operation

#### 3、使用步骤
###### NSOperation和NSOperationQueue实现多线程的具体步骤
* 先将需要执行的操作封装到一个NSOperation对象中
* 然后将NSOperation对象添加到NSOperationQueue中
* 系统会自动将NSOperationQueue中的NSOperation取出来
* 将取出的NSOperation封装的操作放到一条新线程中执行

## 二、NSOperation
#### 1、NSInvocationOperation
###### 建NSInvocationOperation对象
```objc
- (id)initWithTarget:(id)target selector:(SEL)sel object:(id)arg;
```
###### 调用start方法开始执行操作
```objc
- (void)start;
```
**一旦执行操作，就会调用target的sel方法**
###### 注意
* 默认情况下，调用了start方法后并不会开一条新线程去执行操作，而是在当前线程同步执行操作
* 只有将NSOperation放到一个NSOperationQueue中，才会异步执行操作,年代久远，不常用。

#### 2、NSBlockOperation
###### 创建NSBlockOperation对象
```objc
+ (id)blockOperationWithBlock:(void (^)(void))block;
```

###### 通过addExecutionBlock:方法添加更多的操作
```objc
- (void)addExecutionBlock:(void (^)(void))block;
```
**注意：只要NSBlockOperation封装的操作数 > 1，就会异步执行操作**

#### 3、NSOperationQueue
###### NSOperationQueue的作用
```objc
NSOperation可以调用start方法来执行任务，但默认是同步执行的
如果将NSOperation添加到NSOperationQueue（操作队列）中，系统会自动异步执行NSOperation中的操作
```

###### 添加操作到NSOperationQueue中
```objc
- (void)addOperation:(NSOperation *)op;
- (void)addOperationWithBlock:(void (^)(void))block;
```

###### 监听操作完成
```objc
可以监听一个操作的执行完毕
- (void (^)(void))completionBlock;
- (void)setCompletionBlock:(void (^)(void))block;
```

#### 4、最大并发数
###### 什么是并发数
* 同时执行的任务数
* 比如，同时开3个线程执行3个任务，并发数就是3

###### 最大并发数的相关方法
```objc
- (NSInteger)maxConcurrentOperationCount;
- (void)setMaxConcurrentOperationCount:(NSInteger)cnt;
```

#### 5、队列的暂停、取消、恢复
###### 取消队列的所有操作
```objc
- (void)cancelAllOperations;
提示：也可以调用NSOperation的- (void)cancel方法取消单个操作
```

###### 暂停和恢复队列
```objc
- (void)setSuspended:(BOOL)b; // YES代表暂停队列，NO代表恢复队列
- (BOOL)isSuspended;
```

#### 6、操作的优先级
###### 设置NSOperation在queue中的优先级，可以改变操作的执行优先级
```objc
- (NSOperationQueuePriority)queuePriority;
- (void)setQueuePriority:(NSOperationQueuePriority)p;
```

###### 优先级的取值
```objc
NSOperationQueuePriorityVeryLow = -8L,
NSOperationQueuePriorityLow = -4L,
NSOperationQueuePriorityNormal = 0,
NSOperationQueuePriorityHigh = 4,
NSOperationQueuePriorityVeryHigh = 8

iOS8 @property NSQualityOfService qualityOfService

NSQualityOfServiceUserInteractive = 0x21,
NSQualityOfServiceUserInitiated = 0x19,
NSQualityOfServiceUtility = 0x11,
NSQualityOfServiceBackground = 0x09,
NSQualityOfServiceDefault = -1
```

#### 7、操作依赖
###### NSOperation之间可以设置依赖来保证执行顺序
```objc
比如一定要让操作A执行完后，才能执行操作B，可以这么写
[operationB addDependency:operationA]; // 操作B依赖于操作A
可以在不同queue的NSOperation之间创建依赖关系
```
**注意不能相符依赖**

#### 8、线程间通信
###### 子线程->主线程
```objc
[self.queue addOperationWithBlock:^{
        //子线程
        //do something
        [[NSOperationQueue mainQueue] addOperationWithBlock:^{
            //主线程的任务,操作UI等
        }];
    }];
```
#### 9、自定义NSOperation
###### 重写main方法，把我们想要执行的代码放到该方法中去运行。
```objc
- (void)main;
```

## 3、NSOperation VS GCD
#### 1、GCD
	i.  GCD是iOS4.0 推出的，主要针对多核cpu做了优化，是C语言的技术
	ii. GCD是将任务(block)添加到队列(串行/并行/全局/主队列)，并且以同步/异步的方式执行任务的函数
	iii.GCD提供了一些NSOperation不具备的功能
	1.	一次性执行
	2.	延迟执行
	3.	调度组
#### 2、NSOperation
	i.  NSOperation是iOS2.0推出的，iOS4之后重写了NSOperation
	ii.	NSOperation将操作(异步的任务)添加到队列(并发队列)，就会执行指定操作的函数
	iii.NSOperation里提供的方便的操作
	1.	最大并发数
	2.	队列的暂定/继续
	3.	取消所有的操作
	4.	指定操作之间的依赖关系(GCD可以用同步实现)




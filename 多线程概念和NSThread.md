---
title: 多线程概念和NSThread
date: 2016-07-04 10:31:23
tags:
categories: 网络与多线程
---


## 一、多线程的基本概念
#### 1、多线程的基本概念
1. 同步:顺序执行
2. 异步:同时运行
3. 进程:正在运行的程序,一个或多个线程组成
4. 线程:最基本的执行单元

#### 2、特征
* (单核CPU)同一时间,cpu只能处理1个线程,只有1个线程在执行 
* 多线程同时执行:是CPU快速的在多个线程之间的切换
* cpu调度线程的时间足够快,就造成了多线程的“同时”执行
* 如果线程数非常多,cpu会在n个线程之间切换,消耗大量的cpu资源 
* 每个线程被调度的次数会降低,线程的执行效率降低 

###### 多线程的优点
* 能**适当**提高程序的执行效率
* 能**适当**提高资源的利用率（CPU&内存）
* 线程上得任务执行完后自动销毁

###### 多线程的缺点
* 开启线程需要占用一定的内存空间(默认情况下,每一个线程都占512KB) 
* 如果开启大量的线程,会占用大量的内存空间,降低程序的性能
* 线程越多,cpu在调用线程上的开销就越大
* 程序设计更加复杂,比如线程间的通信、多线程的数据共享 
* **时间成本和空间成本**

###### 主线程
* 一个程序运行后,默认会开启1个线程,称为“主线程”或“UI线程”
* 主线程一般用来 刷新UI界面 ,处理UI事件(比如:点击、滚动、拖拽等事件) 
* 主线程使用注意 
	* 别将耗时的操作放到主线程中
	* 耗时操作会卡住主线程,严重影响UI的流畅度,给用户一种卡的坏体验

## 二、NSThread
#### 1、创建和启动线程
**一个NSThread对象就代表一条线程**

###### 创建、启动线程
```objc	
NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(run) object:nil];
[thread start];
// 线程一启动，就会在线程thread中执行self的run方法
```

###### 其他创建线程方式
1、创建线程后自动启动线程
```objc	
[NSThread detachNewThreadSelector:@selector(run) toTarget:self withObject:nil];
```
2、隐式创建并启动线程
```objc		
[self performSelectorInBackground:@selector(run) withObject:nil];
```
3、上述2种创建线程方式的优缺点

	优点：简单快捷
	缺点：无法对线程进行更详细的设置
#### 2、控制线程状态
###### 启动线程
```objc	
- (void)start; 
```

// 进入就绪状态 -> 运行状态。当线程任务执行完毕，自动进入死亡状态

###### 阻塞（暂停）线程
```objc	
+ (void)sleepUntilDate:(NSDate *)date;
+ (void)sleepForTimeInterval:(NSTimeInterval)ti;
```
// 进入阻塞状态

###### 强制停止线程
```objc	
+ (void)exit;
```
// 进入死亡状态

**注意：一旦线程停止（死亡）了，就不能再次开启任务**

#### 3、常用属性方法
###### 获得当前线程
```objc	
NSThread *current = [NSThread currentThread];
```
###### 线程的调度优先级
```objc	
+ (double)threadPriority;
```
调度优先级的取值范围是0.0 ~ 1.0，默认0.5，值越大，优先级越高
###### 线程的名字
```objc	
- (void)setName:(NSString *)n;
- (NSString *)name;
```
###### 主线程相关用法
```objc	
+ (NSThread *)mainThread; // 获得主线程
- (BOOL)isMainThread; // 是否为主线程
```
#### 4、多线程的安全隐患
###### 资源共享
* 1块资源可能会被多个线程共享，也就是多个线程可能会访问同一块资源
* 比如多个线程访问同一个对象、同一个变量、同一个文件
* 当多个线程访问同一块资源时，很容易引发数据错乱和数据安全问题

#### 5、安全隐患解决 – 互斥锁
###### 互斥锁使用格式
	@synchronized(锁对象) { // 需要锁定的代码  }
**注意：锁定1份代码只用1把锁，用多把锁是无效的**
###### 互斥锁的优缺点
	优点：能有效防止因多线程抢夺资源造成的数据安全问题
	缺点：需要消耗大量的CPU资源
**互斥锁的使用前提：多条线程抢夺同一块资源**
###### 相关专业术语：线程同步
	线程同步的意思是：多条线程按顺序地执行任务
	互斥锁，就是使用了线程同步技术
#### 6、原子和非原子属性
###### OC在定义属性时有nonatomic和atomic两种选择
* atomic：原子属性，为setter方法加锁（默认就是atomic）
* nonatomic：非原子属性，不会为setter方法加锁

#### 7、线程间通信
###### 什么叫做线程间通信
在1个进程中，线程往往不是孤立存在的，多个线程之间需要经常进行通信
###### 线程间通信的体现
* 1个线程传递数据给另1个线程
* 在1个线程中执行完特定任务后，转到另1个线程继续执行任务

###### 线程间通信常用方法
```objc	
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait;
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait;
```

#### 8、什么时候使用自动释放池
**If you write a loop that creates many temporary objects.**  
You may use an autorelease pool block inside the loop to dispose of those objects before the next iteration. Using an autorelease pool block in the loop helps to reduce the maximum memory footprint of the application. 

循环中创建了许多临时对象，在循环里面使用自动释放池，用来减少高内存占用。

**If you spawn a secondary thread.**  
You must create your own autorelease pool block as soon 
as the thread begins executing; otherwise, your application will leak objects. (See Autorelease Pool Blocks and Threads for details.) 

开启子线程的时候要自己创建自动释放池，否则可能会发生内存泄露

###### 自动释放池常见面试代码
```objc	
for (int i = 0; i < largeNumber; ++i) {
    NSString *str = @"Hello World";
    str = [str stringByAppendingFormat:@" - %d", i];
    str = [str uppercaseString];
    NSLog(@"%@", str);
}
```
**在for循环内部加入一个自动释放池**
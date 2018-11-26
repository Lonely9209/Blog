---
title: autorelease
date: 2016-07-13 10:32:18
tags:
categories: OC
---

## 一、autorelease基本使用
#### 1.自动释放池及autorelease介绍
* **1.自动释放池**
 	* (1)在iOS程序运行过程中,会创建无数个池子,这些池子都是以栈结构(先进后出)存在的。
 	* (2)当一个对象调用autorelease时,会将这个对象放到位于栈顶的释放池中
* **2.自动释放池的创建方式**
	```objc
	 (1)iOS 5.0以前的创建方式
	     NSAutoreleasePool *pool=[[NSAutoreleasePool alloc] init];
	     // 代码
	     [pool release];  //[pool drain];用于mac
	
	 (2)iOS5.0以后
	 @autoreleasepool
	 {
	   //开始代表创建自动释放池
	 } //结束代表销毁自动释放池
	```
* **3.autorelease**
 	* 是一种支持引用计数的内存管理方式
	* 它可以暂时的保存某个对象(object),然后在内存池自己的排干(drain)的时候对其中的每个对象发送release消息。
 	* 注意,这里只是发送release消息,如果当时的引用计数(reference-counted)依然不为0,则该对象依然不会被释放。可以用该方法来保存某个对象,也要注意保存之后要释放该对象。

#### 2、为什么会有autorelease？
* OC的内存管理机制中比较重要的一条规律是:谁申请,谁释放
 * 考虑这种情况,如果一个方法需要返回一个新建的对象,该对象何时释放?
 * 方法内部是不会写release来释放对象的,因为这样做会将对象立即释放而返回一个空对象;
 * 调用者也不会主动释放该对象的,因为调用者遵循“谁申请,谁释放”的原则。那么这个时候,就发生了内存泄露。
* 不使用autorelease存在的问题
 * 针对这种情况,Objective-C的设计了autorelease,既能确保对象能正确释放,又能返回有效的 对象。
* 使用autorelease的好处
 * (1)不需要再关心对象释放的时间
 * (2)不需要再关心什么时候调用release

####3.autorelease基本用法

* **基本用法**
 * (1)会将对象放到一个自动释放池中
 * (2)当自动释放池被销毁时,会对池子里的所有对象做一次release
 * (3)会返回对象本身
 * (4)调用完autorelease方法后,对象的计数器不受影响(销毁时影响)
 * 在autorelease的模式下,下述方法是合理的,即可以正确返回结果,也不会造成内存泄露。
	```objc
	ClassA *Func1()
	{
	  ClassA *obj = [[[ClassA alloc]init]autorelease];
	  return obj;
	}
	```

#### 4、autorelease的原理？
 * autorelease实际上只是把对release的调用延迟了,对于每一个Autorelease,系统只是把该Object放入了当前的Autorelease pool中,当该pool被释放时,该pool中的所有Object会被调用Release。

#### 5、autorelease什么时候被释放？
* 对于autorelease pool本身,会在如下两个条件发生时候被释放
 * 1)手动释放Autorelease pool
 * 2)Runloop结束后自动释放
 * 对于autorelease pool内部的对象
 * 在引用计数的retainCount ==0的时候释放。release和autorelease pool的 drain都会触发retain--事件。


## 二、autorelease的应用场景
#### 1、autorelease的应用场景
* 经常用来在类方法中快速创建1个对象。

	```objc
	// 声明实现一个类方法
	 + (Student *)student
	{
	    // 在里面直接进行autorelease
	    return [[[Student alloc] init] autorelease];
	}
	```
* 应用：

	```objc
	// 在自动释放池中使用类方法创建对象
	@autoreleasepool{
	 // 此时创建出来的对象不用关注释放问题。
	  Student *s = [Student student];
	}
	```
* 错误写法：

	 ```objc
	 int main(){
	 // 在自动释放池中使用类方法创建对象
	 @autoreleasepool{
	
	 }
	 // 写在自动释放池外部将无法释放对象。
	  Student *s = [Student student];
	  reutrn 0;
	 }
	 ```
#### 2、完善快速创建对象的方法

* 问题1：如果定1个GoodStudent，继承自Student,此时，还能使用类方法快速创建对象吗？

 * 解决方案：
   * 在类方法中使用id

   ```objc
   + (id)student
{
    return [[[Student alloc] init] autorelease];
}
```
`此时，返回的对象仍旧是Student.所以，应该用self，替代Student.`
* 问题2：用其他对象类型，接受自定义对象类型。
* 如：
        
        NSString *s = [Student student];
        NSLog(@"%lu",s.length);
`这段代码，编译时，不会报任何警告，但是运行时会直接崩溃。`
* 改进办法：

  ```objc
  //instancetype：可以动态判断返回的类型和接受的类型是否一致
  + (instancetype)student{

    return [[self alloc] init];
}
```
* 此时，编译器会警告
        NSString *s = [Student student];
        NSLog(@"%lu",s.length);


## 三、autorelease注意
#### 1、autorelease使用注意
##### 并不是放到自动释放池中，都会自动加入到自动释放池
* 1、因为没有调用autorelease方法，所以对象没有加入到自动释放池.

	```objc
	int main(){
	   @autoreleasepool{
	    Student *s = [[Student alloc] init];
	    [s release]; // 正常释放
	   }
	  return 0;
	 }
	 ```
* 2、在自动释放池的外部发送autorelease不会被加入到自动释放池中。

	```objc
	int main(){
	   @autoreleasepool{
	
	   }
	   // 发送autorelease消息的对象，放到制动释放池外部
	   // 此时无法被自动释放
	  Student *s = [[[Student alloc] init] autorelease];
	  return 0;
	 }
	 ```
* 3、不管对象是在自动释放池里创建，还是自动释放池外创建，只要在自动释放池内写1个[s autorelease];s就会被放到自动释放池中，注意：autorelease是一个方法，且只有在自动释放池中使用才有效。

	```objc
	int main(){
	     // 不管在自动释放池内部还是外部创建
	     Student *s = [[Student alloc] init];
	     @autoreleasepool{
	         [s autorelease]; // 此时s加入到释放池
	   }
	   return 0;
	 }
	 ```
* 2）自动释放池的嵌套使用
 * 自动释放池是栈结构。
 * 栈：先进后出。后进先出，
 
	```objc
	int main(int argc, const char * argv[]) {
	    @autoreleasepool {
	               // 第一个池子，里面创建no的1学生
	                Student *s = [[[Student alloc] init] autorelease];
	                s.no = 1;
	        @autoreleasepool {
	                // 第二个池子，里面创建no2的学生
	                   Student *s2 = [[[Student alloc] init] autorelease];
	                   s2.no = 2;
	            @autoreleasepool {
	                // 第二个池子，里面创建no3的学生
	                        Student *s3 = [[[Student alloc] init] autorelease];
	                        s3.no = 3;
	            }
	        }
	    }
	    return 0;
	}
	```
  * 释放顺序：s3,s2,s1
* 3）自动释放池中不适合放占用内存空间较大的对象
 * 1> 尽量避免对大内存使用该方法，对于这种延迟释放机制，尽量少用
 * 2> 不要把大量循环操作放到同1个自动释放池中，这样会造成内存峰值的上升。




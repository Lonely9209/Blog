title: Protocol（协议）
date: 2016-07-14 10:31:23
tags:
categories: OC
---

## 一、Protocol概念及基本使用
#### 1、什么是Protocol?
 * 1）Protocol中文意思：协议。
 * 2）协议中只能声明一些方法。
 * 3）遵守了协议，就等于拥有了协议中的所有方法。

#### 2、协议的基本用法：
 * 1、协议的定义格式：
	```objc
	  @protocol 协议名称
	  // 各种方法；
	  @end
	 ```
 * 2、协议的使用格式：
	```objc
	 @interface 类名 : NSObject <协议名1，协议名2，...>
	 @end
	 ```
	 
	 
## 二、protocol类型限制
#### 1、protocol类型限制

###### 类型限制：
```objc
id d = [[Person alloc] init];

要求：变量d指向的对象必须遵守PlayProtocol协议
id<PlayProtocol> d = [[Person alloc] init];

要求：要求Person创建出来的对象，必须遵守PlayProtocol协议。
Person < PlayProtocol > *p = [[Person alloc] init];

真实使用场景:
@interface Person :NSObject <PlayProtocol>
@end
这样创建的任何对象，都拥有协议当中的方法。

```

#### 2、id和instancetype的区别
```objc
创建对象：
(id)person{
    return [[Person alloc] init];
}

这样，子类无法用这个方法创建对象：
(id)person{
    return [[self alloc] init];
}

调用自己没有的方法，程序会崩溃
建议使用：
(instanstype)person{
    return [[self alloc] init];
}

```
##### 总结：
* id和instancetype的区别
 * 1）instancetype只能作为函数或者方法的返回值
 * 2）id能作为方法或者参数的数据类型、返回值，也能用来定义变量。
 * 3）instancetype对比id的好处：
    * 能精确的限制返回值的具体类型。

 
## 三、protocol其他用法
#### 1、protocol的使用注意
 * 1）把同一类方法，放到1个协议当中
 * 2）1个类可以同时遵守多个协议。
 * 3）父类遵守了某个协议，子类也遵守了该协议。
 * 4）协议遵守协议：1个协议可以遵守多个协议。

#### 2、protocol基协议介绍

 ```objc
@protocol SportProtocol <NSObject>
@end

 * 此处的NSObject就是基协议，意义等同于继承NSObject。
 * 继承基协议的好处：能够拥有基协议当中所声明的所有方法。
```
* 总结：
* 1、需要添加成员变量和方法时，建议使用继承
* 2、只需要添加方法时，建议使用分类
* 3、当很多不同类中，有共同的方法可以抽取时，类之间不存在继承关系，建议使用协议。

## 四、protocol中@required和@optional
* @required：用@required修饰的方法，必须实现。
* @optional：用@optional修饰的方法，可实现，可不实现。
* @required 和 @optional的主要意义:一般用于序员交流用。




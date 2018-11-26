---
title: Copy
date: 2016-07-05 10:32:54
tags:
categories: OC
---

## 一、copy概念及入门
#### 1、【理解】copy的概念
 * Copy的字面意思是“复制”、“拷贝”，是一个产生副本的过程。
 * __常见的复制有：文件复制__
 * 作用：利用一个源文件产生一个副本文件
 * 特点：
 	* 修改源文件的内容，不会影响副本文件
   	* 修改副本文件的内容，不会影响源文件
* __ OC中的copy__
 * 作用：利用一个源对象产生一个副本对象
 * 特点：
   * 修改源对象的属性和行为，不会影响副本对象
   * 修改副本对象的属性和行为，不会影响源对象

#### 2、copy快速入门
 * 如何使用copy功能  
一个对象可以调用copy或mutableCopy方法来创建一个副本对象
  * copy : 创建的是不可变副本(如NSString、NSArray、NSDictionary)
  * mutableCopy :创建的是可变副本(如NSMutableString、NSMutableArray、NSMutableDictionary)
* 使用copy功能的前提  
copy : 需要遵守NSCopying协议，实现copyWithZone:方法

	 ```objc
	 @protocol NSCopying
	 - (id)copyWithZone:(NSZone *)zone;
	@end
	```
 	mutableCopy :需要遵守NSMutableCopying协议，实现mutableCopyWithZone:方法

	 ```objc
	 @protocol NSMutableCopying
	 - (id)mutableCopyWithZone:(NSZone *)zone;
	 @end
	 ```

## 二、copy与内存管理
#### 1、copy与内存管理
 * copy一次，retainCount +1
 
#### 2、深浅拷贝总结
 * 深复制（深拷贝，内容拷贝，deep copy）
   * 源对象和副本对象是不同的两个对象,源对象引用计数器不变, 副本对象计数器为1（因为是新产生的）
   * 本质是：产生了新的对象

 * 浅复制（浅拷贝，指针拷贝，shallow copy）
   * 源对象和副本对象是同一个对象，源对象（副本对象）引用计数器 + 1, 相当于做一次retain操作
   * 本质是：没有产生新的对象

`只有源对象和副本对象都不可变时，才是浅复制，其它都是深复制
`

## 三、@property中的copy的关键字
#### 1、@property中的copy作用

```objc
分析代码:
//创建可变字符串
NSMutableString *str = [NSMutableString string]; //设定字符串的内容
str.string = @"zhangsan";
//创建对象
Person *person = [Person new]; //给person的实例变量赋值 person.name = str;
//修改字符串内容
[str appendString:@"xxxx"];
NSLog(@"name = %@",person.name); NSLog(@"str = %@",str);
// 输出结果：
name = zhangsanxxxx
str = zhangsanxxxx

这显然不符合我们的要求,因为str修改后,会影响person.name 的值 解决方法:
@property (nonatomic,copy) NSString *name;
set方法展开形式为:
- (void)setName:(NSString *)name
{
  if (_name != name)
{
  [_name release];
  [_name release];
  _name = [name mutableCopy];
} }
```

#### 2、@property内存管理策略选择
* @property内存管理策略的选择
 * 1.非ARC
    * 1> copy : 只用于NSString\block
    * 2> retain : 除NSString\block以外的OC对象
    * 3> assign:基本数据类型、枚举、结构体(非OC对象),当2个对象相互引用,一端用retain,一端用assign
 * 2.ARC
    * 1> copy : 只用于NSString\block
    * 2> strong : 除NSString\block以外的OC对象
    * 3> weak : 当2个对象相互引用,一端用strong,一端用weak
    * 4> assgin : 基本数据类型、枚举、结构体(非OC对象)


## 四、为自定义的类实现copy操作
#### 1.为自定义类实现copy操作
##### 自定义对象copy步骤
* 新建Person类
* 为Person类实现copy操作
 * 1.让Person类遵守NSCopying协议
 * 2.实现 copyWithZone:方法,在该方法中返回一个对象的副本即可。
 * 3.在copyWithZone方法中,创建一个新的对象,并设置该对象的数据与现有对象一致, 并返回该对象.
 * 创建Person对象, 调用copy方法, 查看地址。
* 细节介绍:
 * 1、 调用copy其实就是调用copyWithZone方法,所以要实现copyWithZone方法。(查看NSObject协议中的copy方法的介绍)
 * 2、 copyWithZone方法返回值类型是id类型,需要返回一个对象的副本。
 * 3、 关于copyWithZone的参数zone问题:
   *  zone: 表示空间,分配对象是需要内存空间的,如果指定了zone,就可以指定新建对象对应的内存空间。但是:zone是一个非常古老的技术,为了避免在堆中出现内存碎片而使用的。在今天的开发中,zone几乎可以忽略
   *  查看NSCopying协议中的allocWithZone:方法介绍(zone参数可以被忽略,是历史 原因)
  *  4、如果对象没有 可变/不可变 的版本区别,只要实现 copyWithZone 方法即可.
  *  5、copyWithZone:方法的具体实现:

	```objc
	- (id)copyWithZone:(NSZone *)zone{
	//创建对象
	Person *p1 = [[Person alloc] init]; //用当前对象值给新对象的实例变量赋值 p1.age = self.age;
	//返回新对象
	return p1;
	}
	```
* 为Person类实现mutableCopy操作
 * 1.遵守NSMutableCopying协议
 * 2.实现协议对你的方法

	```objc
	- (id)mutableCopyWithZone:(NSZone *)zone{
	//创建对象
	Person *p1 = [[Person alloc] init]; //用当前对象值给新对象的实例变量赋值 p1.age = self.age;
	//返回新对象
	return p1;
	}
	```





---
title: NSNumber/NSValue的介绍
date: 2016-07-06 10:30:28
tags:
categories: OC
---

## 一、NSNumber的介绍
#### 1、NSNumber的介绍和使用
 * NSArray\NSDictionary中只能存放OC对象,不能存放int\float\double等基本数据类型。

 * 如果真想把基本数据(比如int)放进数组或字典中,需要先将基本数据类型包装成OC对象。
 * 基本数据类型（如int）--`包装`-->OC对象--`放进`-->数组/字典

 * NSNumber可以将基本数据类型包装成对象,这样就可以间接将基本数据类型存进NSArray\NSDictionary中
 * 基本数据类型（如int）--`包装`-->NSNumber对象--`放进`-->数组/字典

#### 2、NSNumber的创建
 * 以前

    ```objc
    + (NSNumber *)numberWithInt:(int)value;
    + (NSNumber *)numberWithDouble:(double)value;
    + (NSNumber *)numberWithBool:(BOOL)value;
 ```
 * 现在
 
	```objc
	@10; 	@10.5; 	 @YES; 	 @(num);
	```
	
#### 3、从NSNumber对象中得到基本类型数据
	
```objc
- (char)charValue;
- (int)intValue;
- (long)longValue;
- (double)doubleValue;
- (BOOL)boolValue;
- (NSString *)stringValue;

- (NSComparisonResult)compare:(NSNumber *)otherNumber;
- (BOOL)isEqualToNumber:(NSNumber *)number;
```

## 二、NSValue的介绍和使用
* **NSNumber是NSValue的子类, 但NSNumber只能包装数字类型**
* **NSValue可以包装任意值，因此,可以用NSValue将结构体包装后, 加入NSArray\NSDictionary中。**

#### 1、常见结构体包装
 * 为了方便 结构体 和NSValue的转换,Foundation提供了以下方法：
 * 将结构体包装成NSValue对象

	 ```objc
	 + (NSValue *)valueWithPoint:(NSPoint)point;
	 + (NSValue *)valueWithSize:(NSSize)size;
	 + (NSValue *)valueWithRect:(NSRect)rect;
	 ```
 * 从NSValue对象取出之前包装的结构体

	 ```objc
	 - (NSPoint)pointValue;
	 - (NSSize)sizeValue;
	 - (NSRect)rectValue;
	 ```

#### 2、任意数据的包装
* NSValue提供了下列方法来包装任意数据
    ```objc	  
	+ (NSValue *)valueWithBytes:(const void *)value objCType:(const char *)type;
	```
 * value参数 : 所包装数据的地址
 * type 参数 : 用来描述这个数据类型的字符串,用@encode指令来生成。
* 从NSValue中取出所包装的数据
	```objc	
    - (void)getValue:(void *)value;
    ```




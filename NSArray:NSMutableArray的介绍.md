---
title: NSArray/NSMutableArray的介绍和基本使用
date: 2016-07-03 10:32:03
tags:
categories: OC
---

# 一、NSArray的介绍和基本使用

### 1、NSArray的基本介绍
 * **1、C数组弊端**
 * C语言中数组的弊端
        int array[4] = {10, 89, 27, 76};
 * 只能存放一种类型的数据
 * 不能很方便地动态添加数组元素
 * 不能很方便地动态删除数组元素
 
 * **2、NSArray常见的创建方式：**
	
	 ```objc
	 + (instancetype)array;
	 + (instancetype)arrayWithObject:(id)anObject;
	 + (instancetype)arrayWithObjects:(id)firstObj, ...;
	 + (instancetype)arrayWithArray:(NSArray *)array;
	
	 + (id)arrayWithContentsOfFile:(NSString *)path;
	 + (id)arrayWithContentsOfURL:(NSURL *)url;
	
	 // 可以将一个NSArray保存到文件中
	 - (BOOL)writeToFile:(NSString *)path  atomically:(BOOL)useAuxiliaryFile;
	 - (BOOL)writeToURL:(NSURL *)url atomically:(BOOL)atomically;
	
	 ```
 * **3、NSArray的使用注意事项**
 * 1、只能存放任意OC对象, 并且是有顺序的。
 * 2、不能存储非OC对象,比如int\float\double\char\enum\struct等。
 * 3、它是不可变的,一旦初始化完毕后,它里面的内容就永远是固定的, 不能删除里面的元素, 也不能再往里面添加元素.
 
### 2、NSArray的常见用法
 * **1、NSArray的检索**
 
 * 获取集合元素个数
        - (NSUInteger)count;
        
 * 获得index位置的元素
        - (id)objectAtIndex:(NSUInteger)index;
        
 * 是否包含某一个元素
        - (BOOL)containsObject:(id)anObject;
        
 * 返回最后一个元素
        - (id)lastObject;
        
 * 返回最后一个元素
        - (id)firstObject;
        
 * 查找anObject元素在数组中的位置(如果找不到，返回-1)
        - (NSUInteger)indexOfObject:(id)anObject;
        
 * 在range范围内查找anObject元素在数组中的位置
        - (NSUInteger)indexOfObject:(id)anObject inRange:(NSRange)range;

 * **3、NSArray给所有元素发消息**
 * 让集合里面的所有元素都执行aSelector这个方法
 
	  ```objc
	  - (void)makeObjectsPerformSelector:(SEL)aSelector;
	  - (void)makeObjectsPerformSelector:(SEL)aSelector withObject:(id)argument;
	```

### 3、NSArray的遍历方法

`遍历, 就是将NSArray里面的所有元素一个一个取出来查看`

* **1、NSArray的普通遍历**

	```objc
	for (int i = 0; i<array.count; i++) {  }
	```
* **2、NSArray的快速遍历**

	```objc
	for (id obj in array) {  }
	```

* **3、NSArray使用block进行遍历**

	 ```objc
	 [array enumerateObjectsUsingBlock:^(id obj, NSUInteger  idx, BOOL *stop) {
	 }];
	 ```
[block](block.md)

### 4、NSArray读写文件
* **1、NSArray数据写入到文件中**

	 ```objc
	 NSArray *array = @[@"a",@"b"];
	 BOOL res = [array writeToFile:@"/Users/sleepingsun/Desktop/test.txt" atomically:YES];
	 NSLog(@"%@",array);
	```

* **2、从文件中读取数据到NSArray中**

	 ```objc
	 NSArray *array1 = [NSArray arrayWithContentsOfFile:@"/Users/sleepingsun/Desktop/test.txt"];
	
	 NSLog(@"%@",array1);
	```


### 5、NSArray与NSString
* **1、把数组元素链接成字符串**

	```objc
	// 这是数组方法
	// 用separator作拼接符将数组元素拼接成一个字符串
	  - (NSString *)componentsJoinedByString:(NSString *)separator;
	```

* **2、字符串分割成一个数组方法**

	 ```objc
	 // 这是NSString的方法
	 // 将字符串用separator作为分隔符切割成数组元素
	 - (NSArray *)componentsSeparatedByString:(NSString *)separator;
	```



# 二、NSMutableArray的介绍和基本使用
### 1、NSMutableArray的基本介绍
 * **1、NSMutableArray是NSArray的子类**。NSArray是不可变的,一旦初始化完毕后, 它里面的内容就永远是固定的,不能删除里面的元素,也不能再往里面添加元素。
 * **2、NSMutableArray是可变的**, 随时可以往里面添加\更改\删除元素

* **3、NSMutableArray添加元素**
 * 添加一个元素
        - (void)addObject:(id)object;
        
 * 添加otherArray的全部元素到当前数组中
        - (void)addObjectsFromArray:(NSArray *)array;
        
 * 在index位置插入一个元素
        - (void)insertObject:(id)anObject atIndex:(NSUInteger)index;

* **4、NSMutableArray删除元素**
 * 删除最后一个元素
        - (void)removeLastObject;
        
 * 删除所有的元素
        - (void)removeAllObjects;
        
 * 删除index位置的元素
        - (void)removeObjectAtIndex:(NSUInteger)index;
        
 * 删除特定的元素
        - (void)removeObject:(id)object;
        
 * 删除range范围内的所有元素
        - (void)removeObjectsInRange:(NSRange)range;
        
* **5、NSMutableArray替换元素**
 * 用anObject替换index位置对应的元素
        - (void)replaceObjectAtIndex:(NSUInteger)index withObject:(id)anObject;
        
 * 交换idx1和idx2位置的元素
        - (void)exchangeObjectAtIndex:(NSUInteger)idx1 withObjectAtIndex:(NSUInteger)idx2;


---
title: NSDictionary/NSMutableDictionary的介绍及使用
date: 2016-07-04 10:32:18
tags:
categories: OC
---

# 一、NSDictionary的介绍及使用
### 1、NSDictionary的介绍
* **1、什么是NSDictionary？**
 * NSDictionary翻译过来叫做”字典”
 * 日常生活中, “字典”的作用:通过一个拼音或者汉字,就能找到对应的详细解释。
* **2、NSDictionary的作用:**
 * 类似通过一个key,就能找到对应的value。
 * NSDictionary是不可变的,一旦初始化完毕,里面的内容就无法修改。

* **3、NSDictionary的创建**

	```objc
	+ (instancetype)dictionary;
	+ (instancetype)dictionaryWithObject:(id)object forKey:(id <NSCopying>)key;
	+ (instancetype)dictionaryWithObjectsAndKeys:(id)firstObject, ...;
	+ (id)dictionaryWithContentsOfFile:(NSString *)path;
	+ (id)dictionaryWithContentsOfURL:(NSURL *)url;
	```
* 4、NSDictionary的常见使用

 * 返回字典的键值对数目
        - (NSUInteger)count;
        
 * 根据key取出value
        - (id)objectForKey:(id)aKey;
        
 * 取出字典中所有的Key/Value
			
		@property (readonly, copy) NSArray<KeyType> *allKeys;
		@property (readonly, copy) NSArray<ObjectType> *allValues;

* 5、NSDictionary的遍历
 * 快速遍历
 
 		for (NSString *key in dict) { }

 * block遍历
        
        [dict enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) { }];


### 2、NSDictionary的简写及文件操作
* **1、NSDictionary 的简写形式**

 * NSDictionary的创建
 * 以前
   
	```objc
	[NSDictionary dictionaryWithObjectsAndKeys:@"Jack", @"name", @"男", @"sex", nil];
	```
  * 现在
  
       	@{@"name": @"Jack", @"sex" : @"男”};

 * NSDictionary获取元素
  * 以前
         
     	[dict objectForKey:@"name”];
  * 现在
         
        dict[@"name”];
* 2、NSDictionary 的文件操作

        - (BOOL)writeToFile:(NSString *)path atomically:(BOOL)useAuxiliaryFile;
        - (BOOL)writeToURL:(NSURL *)url atomically:(BOOL)atomically;




# 二、NSMutableDictionary介绍和使用
* 1、NSMutableDictionary 介绍

* 什么是NSMutableDictionary ?
  * NSMutableDictionary是NSDictionary的子类
  * NSDictionary是不可变的,一旦初始化完毕后,它里面的内容就永远是固定的, 不能删除里面的元素, 也不能再往里面添加元素
  * NSMutableDictionary是可变的,随时可以往里面添加\更改\删除元素.

* 2、NSMutableDictionary 的常用方法
 * 添加一个键值对(会把aKey之前对应的值给替换掉)
        - (void)setObject:(id)anObject forKey:(id <NSCopying>)aKey;
 * 通过aKey删除对应的value
        - (void)removeObjectForKey:(id)aKey;
 * 删除所有的键值对
        - (void)removeAllObjects;

* 3、NSMutableDictionary 的简写
 * 设置键值对
  * 以前
        [dict setObject:@"Jack" forKey:@"name”];

  * 现在
        dict[@"name"] = @"Jack";



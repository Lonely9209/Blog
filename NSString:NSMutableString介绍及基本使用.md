---
title: NSString/NSMutableString介绍及基本使用
date: 2016-07-01 10:32:01
tags:
categories: OC
---

# 一、NSString
### 1、NSString的大小写处理
```objc	
// 全部字符转换为大写字母
- (NSString *)uppercaseString;

// 全部字符转换为小写字母
- (NSString *)lowercaseString;

// 首字母变大写，其他字母都变小写
- (NSString *)capitalizedString;
```

### 2、字符串的比较

* 1、两个字符串的内容相同就返回YES, 否则返回NO
	```objc	
	- (BOOL)isEqualToString:(NSString *)aString;
	```
* 2、这个方法可以用来比较两个字符串内容的大小
	```objc	
	- (NSComparisonResult)compare:(NSString *)string;
	```
* 比较方法: 逐个字符地进行比较ASCII值，只要有一个值能够比较出大小，就不再继续比较。返回NSComparisonResult作为比较结果

	```objc
	NSComparisonResult是一个枚举，有3个值:
	如果左侧   < 右侧,返回NSOrderedAscending,-1L  递增
	如果左侧   > 右侧,返回NSOrderedDescending,    递减
	如果左侧  == 右侧返回NSOrderedSame            相等
	
	//忽略大小写进行比较，返回值与compare:一致
	- (NSComparisonResult) caseInsensitiveCompare:(NSString *)string;
	
	```
* 另外,在使用compare方法时可以添加参数来决定是否区分大小写,或者声明为比较字符串个数而不是字符值等比较条件

	```objc
	 NSString *str = @"aBc";
	 NSString *str1 = @"abc";
	
	 // 忽略大小写字符串比较
	 NSComparisonResult result = [str caseInsensitiveCompare:str1];
	
	// 因为忽略B和b,所以str = str1
	 NSLog(@"%ld",result);
	```
	
### 3、NSString前后缀检查及搜索
* 1、字符串前后缀检查
*  前缀检查--是否以aString开头
	
	```objc
	- (BOOL)hasPrefix:(NSString *)aString;
	
	```
*  后缀检查--是否以aString结尾
	
	```objc
	- (BOOL)hasSuffix:(NSString *)aString;
	
	```
* 2、字符串查找

	```objc
	// 用来检查字符串内容中是否包含了aString
	// 如果包含, 就返回aString的范围
	// 如果不包含, NSRange的location为NSNotFound, length为0
	
	- (NSRange)rangeOfString:(NSString *)aString;
	```
	
### 4、字符串的截取和替换
* 1、字符串的截取
 * 从指定位置from开始(包括指定位置的字符)到尾部
	```objc	
	- (NSString *)substringFromIndex:(NSUInteger)from;
	```
 * 从字符串的开头一直截取到指定的位置to，但不包括该位置的字符.
    ```objc	
    - (NSString *)substringToIndex:(NSUInteger)to;
	```
 * 按照所给出的NSRange从字符串中截取子串
    ```objc	
    - (NSString *)substringWithRange:(NSRange)range;
    ```
 * 返回数组
	```objc		
	- (NSArray<NSString *> *)componentsSeparatedByString:(NSString *)separator;
	- (NSArray<NSString *> *)componentsSeparatedByCharactersInSet:(NSCharacterSet *)separator;
	```
* 2、字符串的替换函数
 * 用replacement替换target
	```objc	
	- (NSString *)stringByReplacingOccurrencesOfString:(NSString *)target withString:(NSString *)replacement;
	```
* 3、字符串与路径
 * 是否为绝对路径
	```objc	
	- (BOOL)isAbsolutePath;
    ```
 * 获得最后一个目录
    ```objc	
    - (NSString *)lastPathComponent;
	```
 * 删除最后一个目录
    ```objc	
    - (NSString *)stringByDeletingLastPathComponent;
	```
 * 在路径的后面拼接一个目录
    ```objc	
    - (NSString *)stringByAppendingPathComponent:(NSString *)str;
  	也可以使用stringByAppendingString:或者stringByAppendingFormat:拼接字符串内容
  	```
* 4、字符串与文件扩展名
 * 获得扩展名
    ```objc	
    - (NSString *)pathExtension;
    ```
 * 删除尾部的拓展名 
    ```objc
    - (NSString *)stringByDeletingPathExtension;
    ```
 * 在尾部添加一个拓展名
    ```objc
    - (NSString *)stringByAppendingPathExtension:(NSString *)str;
    ```
    
### 5、NSString的其他用法
* 1、获得字符串的长度
	```objc	
	- (NSUInteger)length;
    ```
* 2、获得具体位置的字符
    ```objc	
    - (unichar)characterAtIndex:(NSUInteger)index;
    返回index位置对应的字符,%c输出
    ```

* 3、字符串和其他数据类型转换
 * 转为基本数据类型

	```objc
	- (double)doubleValue;
	- (float)floatValue;
	- (int)intValue;
	```
 * 转为C语言中的字符串(数据库)

	```objc
	- (char *)UTF8String;
	```
* 4、NSString去除空格

 * 去除所有的空格
 
	```objc
	[str stringByReplacingOccurrencesOfString:@" " withString:@""]
	```
 * 去除首尾的空格
 
	```objc
	[str stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];
	```
* 5、中文使用%转义( 网络访问请求：中文空格字符编码/解码)
	```objc	
	stringByAddingPercentEscapesUsingEncoding(只对 `#%^{}[]|\"<> 加空格共14个字符编码，不包括”&?”等符号), ios9将淘汰，建议用stringByAddingPercentEncodingWithAllowedCharacters方法
	
	- (nullable NSString *)stringByAddingPercentEscapesUsingEncoding:(NSStringEncoding)enc （已过期使用下面方法）
	
	- (nullable NSString *)stringByAddingPercentEncodingWithAllowedCharacters:(NSCharacterSet *)allowedCharacters;

	URLFragmentAllowedCharacterSet  "#%<>[\]^`{|}
       URLHostAllowedCharacterSet      "#%/<>?@\^`{|}
       URLPasswordAllowedCharacterSet  "#%/:<>?@[\]^`{|}
       URLPathAllowedCharacterSet      "#%;<>?[\]^`{|}
       URLQueryAllowedCharacterSet     "#%<>[\]^`{|}
       URLUserAllowedCharacterSet      "#%/:<>?@[\]^`
	[NSCharacterSet characterSetWithCharactersInString:@"`#%^{}\"[]|\\<> "].invertedSet];
	[NSCharacterSet URLQueryAllowedCharacterSet]
	```
	
# 二、NSMutableString
### 1、NSMutableString常用方法
* 1、NSMutableString常用方法

* 拼接aString到最后面
	```objc	
	- (void)appendString:(NSString *)aString;
	```
* 拼接一段格式化字符串到最后面
	```objc	
	- (void)appendFormat:(NSString *)format, ...;
	```
* 删除range范围内的字符串
	```objc	
	- (void)deleteCharactersInRange:(NSRange)range;
	```
* 在loc这个位置中插入aString
	```objc	
	- (void)insertString:(NSString *)aString atIndex:
(NSUInteger)loc;
	```
* 使用aString替换range范围内的字符串
	```objc	
	- (void)replaceCharactersInRange:(NSRange)range withString:(NSString *)aString;
    ```
**注:**
```objc	
字符串中的可变和不可变
* NSMutableString和NSString的区别
* NSString是不可变的,里面的文字内容是不能进行修改的。
* NSMutableString是可变的,里面的文字内容可以随时更改。
* NSMutableString能使用NSString的所有方法
* 注: NSMutableString是可变字符串，是NSString的子类。
```

### [使用NSURL读写字符串](https://lonely9209.github.io/2016/07/02/使用NSURL读写字符串/)
### [NSDate的介绍](https://lonely9209.github.io/2016/07/09/NSDate的介绍/)
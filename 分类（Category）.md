---
title: 分类（Category）
date: 2016-07-15 10:31:23
tags:
categories: OC
---

## 一、分类（Category）概念及使用流程
```objc
// 学习引入：
@interface Person :NSObject 
-(void)run;
-(void)eat;
-(void)study;
@end -(void)playlol;
```

#### 1、分类的概念及作用
##### 1、分类的概念：
   * Category：类别、类目、分类。

##### 2、分类的作用
 * 1）将1个类中的不同方法分到多个不同的文件中存储，便于对类进行模块化设计和团队开发。
 * 2）可以在不修改原来类的基础上，为这个类扩充一些方法。
 * 3）注意：
   * 分类中只能增加“方法”，不能增加成员变量。
   * 分类中可以访问原来类中的成员变量。

##### 3、使用分类的目的
 * 1）对现有类进行扩展:
   * 比如,你可以扩展Cocoa touch框架中的类,你在类别中增加的方法会被子类所继承,而且在运行 时跟其他的方法没有区别。
 * 2）作为子类的替代手段:
   * 不需要定义和使用一个子类,你可以通过类别直接向已有的类里增加方法。
 * 3）对类中的方法归类:
   * 利用category把一个庞大的类划分为小块来分别进行开发,从而更好的对类中的方法进行更新和维护。

##### 4、分类的使用流程
* 声明分类-->实现分类-->使用分类
* 注意:分类的命名规则:类名+扩展方法,如“NSString+countNum”。 分类的接口声明与类的定义十分相似,但分类不继承父类,只需要带有一个括号,表明该分类的主要用途。

## 二、分类（Category）声明和实现
#### 1、声明和实现一个分类
* 在.h文件中,声明分类:
	
	```objc
	@interface 类名 (分类名)
	//方法;
	@end
	```
* 说明:

	```objc
	声明分类格式
	1）新添加的方法必须写在 @interface 与 @end 之间
	2）ClassName 现有类的类名(要为哪个类扩展方法)。
	3）CategoryName 待声明的分类名称
	4）NewMethod 新添加的方法
	```
##### 注意:
 * 不允许在声明分类的时候定义变量

	  ```objc
	  @interface Person(sport)
	  {
	     int _age; // 报错
	   }
	  - (void)run;
	  - (void)jump;
	
	  ```
__在.m文件中(也可以在.h中实现),实现分类:__

```objc
@implementation ClassName(CategoryName) NewMethod
{
......
}
@end
```
* 说明:
 * 实现类别格式
   * 1)新方法的实现必须写在@ implementation与@end之间
   * 2)ClassName 现有类的类名
   * 3)CategoryName 待声明的分类名称
   * 4)NewMethod 新添加的方法的实现


###### 2、调用分类中的方法
  * 调用分类中的方法与一般方法调用，完全一样（需要导入头文件）

## 三、分类（Catefory）使用注意事项
#### 1、分类的使用注意事项
 * 1）分类只能增加方法, 不能增加成员变量
 * 2）可以书写@property,此时只生成get和set方法,不会生成私有变量
 * 3)如果分类和原来类出现同名的方法, 优先调用分类中的方法, 原来类中的方法会被屏蔽
 
#### 2、分类的编译的顺序
 * 1）当分类与主类中都有同一个方法时，优先调用分类中的方法。
 * 2）当多个分类中都有同样的一个方法时，优先调用最后一个参与编译的分类中的方法。

## 四、分类（Category）非正式协议

* 显然这个名词是相对于正式协议而言的。在解释非正式协议之前,先引用两段话:

* 1、在《Cocoa设计模式》第六章类别的6.3.2把分类用于非正式协议一节中,这样写到:`非正式协议通常定义为NSObject的分类`。分类接口中指定的方法可能会或者可能不会被框架类实际地实现,而是被子类重写。

* 2、苹果官方文档Cocoa CoreCompetencies一文中是这样介绍非正式协议的:An informal protocol is a category on NSObject, which implicitly makes almost all objects adopters of the protocol. (A category is a language feature that enables you to add methods to a class without subclassing it.) Implementation of the methods in an informal protocol is optional. Before invoking a method, the calling object checks to see whether the target object implements it. Until optional protocol methods were introduced in Objective-C 2.0, informal protocols were essential to the way Foundation and AppKit classes implemented delegation.

*  大概意思:非正式协议是NSObject类(显而易见,还包括它的子类)的分类,其所有的子类都含蓄地接受了这个协议。(分类是Objective-C的一个语言特点,可以让你在无需子类化的前提下为一个类增加方法。)非正式协议中的方法是否实现都是可选的,因此在调用非正式协议中的方法之前,需要去检查对象类是否实现了它。在Objective-C2.0中引入可选的正式协议方法之前,非正式协议是Foundation和AppKit类实现委托的唯一方式。
`所谓的非正式协议就是分类,即凡是NSObject或其子类Foundation框架中的类,增加的分类,都是非正式协议。`

* 思考：
 * 统计一个字符串中数字的个数
 * NSString *s = @"dahfkd234234hkj324lk432bkb42";
* 解决方法：遍历字符串中的每个字符，判断是否在0-9之间。
	
	```objc
	@interface NSString (count)
	-(void)countStrNum;
	@end
	@implementation NSString (count)
	-(void)countStrNum
	{
	int count=0;
	
	//遍历字符串
	for(int i=0;i<self.length;i++)
	{
	//获取字符串的每一个字符
	unichar c = [self characterAtIndex:i];
	
	//判断是否是阿拉伯数字
	if (c>='0' && c<='9')
	{
	//如果是阿拉伯数字,则+1
	count++;
	}
	}
	NSLog(@"count = %d",count);
	}
	
	```
	
## 五、分类（Catefory）延展
#### 1、类的延展的概念
 * 延展类别又称为扩展(Extendsion)
 * Extension是Category的一个特例
 * 其名字为匿名(为空),并且新添加的方法一定要予以实现。(Category没有这个限制)
	
	```objc
	@interface MyClass ()
	{
	   //类扩展 float value;
	}
	- (void)setValue:(float)newValue;
	@end
	```
 * 这种写法的类别叫`匿名分类`,又叫`类扩展`,所谓的扩展,其实就是为一个类添加额外的原来没有的变量、方法或者合成属性。

#####2、类扩展的作用：
 * 为本类扩充一些私有的成员变量和私有方法。

##### 3、延展的实现
 * 1）通过延展来实现方法的私有,延展的头文件独立。这种方法不能实现真正的方法私有,当在别的文件中引入延展的头文件,那么在这个文件中定义的类的对象就可以直接调用在延展中定义所谓 私有的方法.

	```objc
	// 对类的延展,隐藏方法的一种手段
	.h文件
	@interface SceneManager ()
	+ (void) wrap;
	@end
	
	.m类的实现文件中
	@implementation SceneManager
	+ (void) wrap {
	  NSLog(@"method of wrap");
	}
	@end
	```
* 调用

	```objc
	[SceneManager wrap];
	// 这里会报一个警告:Class method of "+ wrap" not found
	// 不过虽然是警告,不过运行是正常的,不过这么写在自我规范上不好,即使编译器没有做强制限制,我们自己也要限制自己。
	// 不然,延展方法就毫无意义可言。
	```

* 2）第二种实现延展的方式是延展没有独立的头文件,在类的实现文件.m中声明和实现延展,这种方法可以很好的实现方法的私有,因为在OC中是不能引入.m的文件的。

	```objc
	 .m文件
	@interface SceneManager ()
	+ (void) wrap;
	@end
	
	@implementation SceneManager
	+ (void) wrap {
	NSLog(@"method of wrap"); }
	@end
	调用
	[SceneManager wrap];
	```

##### 分类和类扩展的区别：
* 1、类别中只能增加方法;
* 2、是的,你没看错,类扩展不仅可以增加方法,还可以增加实例变量(或者合成属性),只是该`实例变量默认是私有类型的`(作用范围只能在自身类,而不是子类或其他地方);
* 3、类扩展中声明的方法没被实现,编译器会报警,但是类别中的方法没被实现编译器是不会有任何警告的。这是因为类扩展是在编译阶段被添加到类中,而类别是在运行时添加到类中。
* 4、类扩展不能像类别那样拥有独立的实现部分(@implementation部分),也就是说,类扩展所声明的方法必须依托对应类的实现部分来实现。
* 5、定义在 .m 文件中的类扩展方法为私有的,定义在 .h 文件(头文件)中的类扩展方法为公有 的。类扩展是在 .m 文件中声明私有方法的非常好的方式。

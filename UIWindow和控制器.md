---
title: UIWindow和控制器
date: 2016-07-02 10:31:23
tags:
categories: UI
---

* UIWindow是一种特殊的UIView,通常在一个app中只有一个UIWindow
* iOS程序启动完毕后，创建的第一个视图控件就是UIWindow，接着创建控制器的view，最后将控制器的view添加到UIWindow上，于是控制器的view就显示在屏幕上了。
* -个iOS程序之所以能显示在屏幕上，完全是因为他有UIWindow的存在,也就是说，没有UIWindow,就看不见任何UI界面。
* 可以自己手动创建UIWindow

**注：创建的window只要直接置为nil就会消失**
## 一、window选择控制器方法(category)
```objc
#import "UIWindow+Extension.h"
#import "ZCTabBarController.h"
#import "ZCNewfeatureController.h"

@implementation UIWindow (Extension)

- (void)chooseRootViewController
{
    UIWindow *window = [UIApplication sharedApplication].keyWindow;
    
    NSString *currentVersion = [NSBundle mainBundle].infoDictionary[@"CFBundleShortVersionString"];
    
    //获取本地存储的版本号
    NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
    
    NSString *sandboxVersion = [defaults objectForKey:@"sandboxVersion"];
    
    //比较两个版本号
    // NSOrderedAscending == 升序, NSOrderedSame == 相同, NSOrderedDescending == 降序
    
    //如果是降序
    if ([currentVersion compare:sandboxVersion] == NSOrderedDescending) {
        //0.监听通知
        [ZC_NotificationCenter addObserver:self selector:@selector(changeRootViewController) name:@"chooseRootViewController" object:nil];
        
        //1.显示新特性
        ZCNewfeatureController *newfeatureVC = [[ZCNewfeatureController alloc] init];
        window.rootViewController = newfeatureVC;
        //2.保存新的版本号
        [defaults setValue:currentVersion forKey:@"sandboxVersion"];
    } else {
        
        //2.创建跟控制器
        ZCTabBarController *tabBar = [[ZCTabBarController alloc] init];
        
        //5.设置跟控制器
        window.rootViewController = tabBar;
    }
}

- (void)changeRootViewController
{
    //1.移除观察者身份
    [ZC_NotificationCenter removeObserver:self];
    
    //2.设置跟控制器
    [UIApplication sharedApplication].keyWindow.rootViewController = [[ZCTabBarController alloc] init];
    
}

@end
```
## 二、手动创建控制器
* 不适用Xcode自动生成的ViewController及storyboard文件
* 自己手动实现加载控制器
* 实现步骤
    * 在应用程序加载完毕后，手动创建UIWindow及应用程序代理里面的UIWindow
    * 创建要加载的控制器
    * 设置为窗口的根控制器
    * 设置窗口为主窗口并可见，或者设置为主窗口，然后设置窗口的hidden属性为NO
    
###### 注意：创建的窗口默认是不可见，隐藏的。

```objc
// 1.创建窗口
self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
self.window.windowLevel = UIWindowLevelStatusBar;
NSLog(@"%p", self.window);

// 2.创建自定义的控制器
CZViewController *vc = [[CZViewController alloc] init];

// 3.设置窗口的根控制器为自定义的控制器
self.window.rootViewController = vc;

// 4.设置窗口为主窗口并可见
[self.window makeKeyAndVisible];
```
## 三、创建控制器的方法介绍
* 纯代码创建控制器

* 通过storyboard文件创建控制器

* 通过xib的方式创建控制器

#### 1、纯代码创建控制器
* 1.创建窗口
	```objc
	self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
	```
* 2.创建控制器作为窗口的根控制器
	```objc
	CZViewController *vc = [[CZViewController alloc] init];
	self.window.rootViewController = vc;
	```
* 3.设置窗口为主窗口并可见
	```objc
	[self.window makeKeyAndVisible];
	```
#### 2、通过storyboard文件创建控制器
* 1.创建窗口
	```objc
	self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
	```
* 2.创建控制器作为窗口的根控制器
	* 2.1加载storyboard文件
		```objc
		UIStoryboard *sb = [UIStoryboard storyboardWithName:@"CZStoryboard" bundle:nil];
		```
	* 2.2作为初始化控制器  【文件中只有一个storyboard】
		```objc
	  	UIViewController *vc = [sb instantiateInitialViewController];
		```
 		**注：有多个storyboard，就需要根据标记去加载 【文件中又多个storyboard】**
 		```objc
		UIViewController *vc = [sb instantiateViewControllerWithIdentifier:@"green"];
		```
	* 2.3设置为窗口的根控制器
		```objc
		self.window.rootViewController = vc;
		```
* 3.设置窗口为主窗口并可见
	```objc
	[self.window makeKeyAndVisible];
	```
#### 3、通过xib的方式创建控制器
* 1.创建窗口
	```objc
	self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
	```
* 2.创建控制器作为窗口的根控制器
	* 2.1直接根据xib文件去加载
		```objc
		xib文件名称与控制器名称完全不同
	    1.修改xib文件的File's Owner类型为自定义控制器类型
	    2.选中File's Owner，右键将view连线到xib文件中的view上
	    3.在alloc+init的时候指定要加载的xib文件名称
	    
		CZViewController *vc = [[CZViewController alloc] initWithNibName:@"Empty" bundle:nil];
		```
	* 2.2xib文件名称与控制器名称类似但是少了Controller
	
		```objc
	    1.同上
	    2.同上
	    3.直接alloc + init，不需要指定要加载的文件名称，系统会自动寻找名称与控制器名称类似【少了类型的后缀（如controller）】的xib文件	    
	    CZViewController *vc = [[CZViewController alloc] init];
	    ```

	* 2.3.xib文件名称与控制器名称完全相同
	
		```objc
	    1.同上
	    2.同上
	    3.系统会优先去寻找与控制器名称完全相同的xib文件
	CZViewController *vc = [[CZViewController alloc] init];
	```

	* 2.3设置为窗口的根控制器
		```objc
		self.window.rootViewController = vc;
		```

	* 3.设置窗口为主窗口并可见
		```objc
		[self.window makeKeyAndVisible];
		```
	
## 四、控制器的view是懒加载的方式创建的
* 控制器的view是延迟加载(懒加载)的：用到时再加载
* 控制器的view加载完毕就会调用viewDidLoad方法
* 用isViewLoaded方法判断UIViewController的view是否已经被加载
* 重写loadView方法，如果在窗口显示之前如果没有用到控制器的view就不会调用loadView方法，在窗口显示的时候就会调用loadView方法。
* 如果在窗口显示之前用到了控制器的view，就会直接调用loadView方法。
* loadView方法仅在用代码创建视图控制器的界面时使用
* 在loadView方法中实现加载自定义的view的时候，不需要调用super。

## [UIView](http://www.cnblogs.com/snake-hand/p/3190021.html)
	
	
---
title: UIApplication
date: 2016-07-01 10:31:23
tags:
categories: UI
---


## 一、UIApplication
* 一个UIApplication代表是一个应用程序，而且是**单例**的。
    * 用来封装整个应用程序的一个对象,比如当应用程序执行到某个时期要做什么, 生命周期等。
    * 获取UIApplication对象： [UIApplication sharedApplication];（单例的）
* 设置消息数量及联网状态指示器
* 通过应用程序打开一些资源
    * 如打电话、发短信、发邮件、打开别的应用等
* 状态栏的管理
    * 默认情况下，状态栏的管理都是交给控制器的
    * 可以修改配置文件将状态栏的管理权限交给应用程序统一来管理
    * 在info.plist文件中增加一个key(View controller-based status bar appearance)，设置为NO。（最后一个）
   
#### 获取当前系统版本号
 	NSString *currentVersion = [NSBundle mainBundle].infoDictionary[@"CFBundleShortVersionString"] 

## 二、应用程序代理对象
* 应用序代理对象，用来监听应用程序的执行过程

* 常用监听方法

    * 应用程序加载完毕
        * 可执行自定义操作
    * 应用程序失去焦点
        * 不接受用户交互
    * 应用程序进入后台
        * 双击home键课查看
    * 应用程序进入前台
        * 双击home键，选中程序的时候进入前台
    * 应用程序获取焦点
        * 接受用户交互
    * 应用程序销毁
        * 只有在直接双击home，销毁程序时在会调用
    * 应用程序接收到内存警告
        * 当内存不足时，会收到系统的内存警告

#### 常用代理方法解释
* 应用程序已经加载完成时调用

	```objc
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	重写这个方法可以在程序加载完成后进行自定义操作
	return YES;
	}
	```
* 应用即将辞去激活状态，失去焦点

	```objc
	- (void)applicationWillResignActive:(UIApplication *)application {
	当应用从激活状态变为非激活状态的时会执行这个方法
	在某些情况下会暂时中断（如收到电话或短信）或者当用户退出应用程序并且应用程序进入后台的时候

	使用这个方法去暂停正在执行的任务，停止定时器操作，并且节流OpenGL ES的帧率。游戏应用应该在这个方法中暂停游戏
	OpenGL（全写Open Graphics Library）是个定义了一个跨编程语言、跨平台的编程接口规格的专业的图形程序接口。它用于三维图像（二维的亦可），是一个功能强大，调用方便的底层图形库。
	OpenGL ES (OpenGL for Embedded Systems) 是 OpenGL 三维图形 API 的子集，针对手机、PDA和游戏主机等嵌入式设备而设计。该API由Khronos集团定义推广，Khronos是一个图形软硬件行业协会，该协会主要关注图形和多媒体方面的开放标准
	}
	```
* 应用程序已经进入后台

	```objc
	- (void)applicationDidEnterBackground:(UIApplication *)application {	
	使用这个方法去释放共享的资源，保存用户数据，销毁定时器，并且保存足够的应用程序状态信息，防止用户稍后将程序销毁而无法恢复到应用当前的状态。
	如果你的应用支持后台执行，当用户退出销毁应用程序的时候，会调用这个方法，而不是applicationWillTerminate:方法
	}
	```
* 应用程序即将进入前台

	```objc
	- (void)applicationWillEnterForeground:(UIApplication *)application {	
	
	作为应用程序从后台到非活跃状态的一个过渡，在这里可以撤销许多在程序进入后台时做出的改变。
	}
	```
* 应用程序已经变为活跃状态

	```objc
	- (void)applicationDidBecomeActive:(UIApplication *)application {
	重新启动任何在应用程序为非活跃状态时暂停的任务，如果应用程序在后太执行，那么界面是否刷新都是可以的
	}
	```
* 程序即将被销毁

	```objc
	- (void)applicationWillTerminate:(UIApplication *)application {
		在应用程序即将被销毁时调用，如果合适，保存数据
	   	另请参阅“应用程序进入后台”的方法
	}
	```

## 三、应用启动方法执行过程
 * 当应用程序启动后：
    * didFinishLaunchingWithOptions
    * applicationDidBecomeActive
 * 当应用程序进入后台：
    * applicationWillResignActive
    * applicationDidEnterBackground
 * 当应用程序从后台进入前台：
    * applicationWillEnterForeground
    * applicationDidBecomeActive
 * 当应用被销毁时：
    * [在应用程序打开时，直接通过双击"home"键，模拟器command+shift+双击"h"]
    * applicationWillResignActive
    * applicationDidEnterBackground
    * applicationWillTerminate
    * [先按一下"home"键，模拟器command+shift+"h"键，先让程序进入后台]
    * applicationWillResignActive
    * applicationDidEnterBackground
    * 之后再销毁程序的时候不会执行任何方法，程序崩溃

## 四、设置消息数量及联网状态指示器

* 当一个iOS程序启动后，首先创建的第一个对象就是UIApplication对象。
* 利用UIApplication可以做一些应用级别的操作。
    * 应用级别操作：
        * QQ有消息的时候右上角的消息条数。
        
        ```objc
        // 获取UIApplication对象。
        UIApplication *app = [UIApplication sharedApplication];

        // 设置右上角, 有10条消息
        app.applicationIconBadgeNumber = 10;

        // 取消显示消息
        app.applicationIconBadgeNumber = 0;
        ```

	```objc
	// 当点击按钮时, 设置右上角消息
	- (IBAction)click:(id)sender {
	
	// 获取UIApplication对象
	UIApplication *app = [UIApplication sharedApplication];
	
	// iOS 8 系统要求设置通知的时候必须经过用户许可。
	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeBadge categories:nil];
	
	[app registerUserNotificationSettings:settings];
	
	app.applicationIconBadgeNumber = 10; // 有10条消息
	
	//  app.applicationIconBadgeNumber = app.applicationIconBadgeNumber > 0 ? 0 : 10; // 有10条消息
	}
	```
* 联网操作时，状态栏上的等待图标指示器。waiting图标。

	```objc
	UIApplication *app = [UIApplication sharedApplication]; app.networkActivityIndicatorVisible = YES;
	
	```
	
## 五、打开一些资源
* 利用UIApplication打开某个资源：

	```objc
	// 系统会自动根据协议识别使用某个app打开。
	UIApplication *app = [UIApplication sharedApplication];
	// 打开一个网页:
	[app openURL:[NSURL URLWithString:@"http://ios.icast.cn"]];
	
	// 打电话
	[app openURL:[NSURL URLWithString:@"tel://10086"]];
	
	// 发短信
	[app openURL:[NSURL URLWithString:@"sms://10086"]];
	
	// 发邮件
	[app openURL:[NSURL URLWithString:@"mailto://12345@qq.com"]];
	```
* 使用openURL方法也可以打开其他应用，在不同应用之间互相调用对方。
    * 美图秀秀, 点击分享到"新浪微博", 打开"新浪微博"选择账号, 跳转回"美图秀秀", 开始分享
    * 喜马拉雅, 使用微博、QQ 账号 登录。都需要应用程序间跳转。
    
## 六、状态栏的管理
* 理状态栏的管理：
    * 自从iOS7开始可以通过两种方式来控制状态栏
        * 控制器
        * 通过UIViewController管理（每一个UIViewController都可以拥有自己不同的状态栏）

	```objc
	#pragma mark - 通过控制器来管理状态栏是否显示及显示类型
	// 是否隐藏"状态栏"
	- (BOOL)prefersStatusBarHidden {
	    return NO;
	}
	
	// 状态栏的样式
	- (UIStatusBarStyle)preferredStatusBarStyle
	{
	    // 白色
	    return UIStatusBarStyleLightContent;
	}
	```

* UIApplication
    * 通过UIApplication管理（一个应用程序的状态栏都由它统一管理）

    * iOS7开始状态栏默认交给了控制器来管理，如果希望通过UIApplication来管理，步骤如下:
    * 在Info.plist文件中增加一个配置项
        * View controller-based status bar appearance = NO

	```objc
	#pragma mark - 通过应用程序来管理状态栏
	-  (IBAction)click:(id)sender {
	
	    UIApplication *app = [UIApplication sharedApplication];
	    // 设置状态栏是否隐藏                                    //app.statusBarHidden = YES;
	    // 动画的方式
	    [app setStatusBarHidden:YES withAnimation:UIStatusBarAnimationSlide];
	
	    // 设置状态栏显示为白色
	    // app.statusBarStyle = UIStatusBarStyleLightContent;
	    // 动画的方式
	    [app setStatusBarStyle:UIStatusBarStyleLightContent animated:YES];
	}
	```




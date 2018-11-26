---
title: UINavigationController
date: 2016-07-05 10:31:23
tags:
categories: UI
---

## 一、UINavigationController的基本使用
* 初始化UINavigationController
* 设置UIWindow的rootViewController为UINavigationController
* 将第一个视图控制器设置为UINavigationController的根视图控制器
* 通过push方法新建子控制器
* 通过pop方法可以返回到上一个控制器


#### 1、UINavigationController子控制器
* 栈内所有子控制器的集合
	```objc
	@property(nonatomic,copy) NSArray<__kindof UIViewController *> *viewControllers;
	```
* 栈顶控制器
	```objc
	@property(nullable, nonatomic,readonly,strong) UIViewController *topViewController;
	```
* 通过push方法将控制器压栈  
	```objc
	- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated;
	```
* 通过pop方法将栈顶控制器移出栈
	```objc
	- (nullable UIViewController *)popViewControllerAnimated:(BOOL)animated
	```
	
* 回到指定控制器
	```objc
	- (NSArray *)popToViewController:(UIViewController *)viewController animated:(BOOL)animated;
	```
* 回到根控制器
	```objc
	- (NSArray *)popToRootViewControllerAnimated:(BOOL)animated;
	```
	
#### 2、修改导航栏的内容
###### 导航栏的内容由栈顶控制器的navigationItem属性决定
###### UINavigationItem有以下属性影响着导航栏的内容
* 左上角的返回按钮
	```objc
	@property(nonatomic,retain) UIBarButtonItem *backBarButtonItem;
	```
* 中间的标题视图
	```objc
	@property(nonatomic,retain) UIView          *titleView;
	```
* 中间的标题文字
	```objc
	@property(nonatomic,copy)   NSString        *title;
	```
* 左上角的按钮
	```objc
	@property(nonatomic,retain) UIBarButtonItem *leftBarButtonItem;
	```
* 右上角的按钮
	```objc
	@property(nonatomic,retain) UIBarButtonItem *rightBarButtonItem;
	```
	
#### 当子控制器重新设置了返回键怎样保留手势退出功能
```objc
//保留pop手势（清空代理，让控制器重新设置这个功能滑动移除控制器）
self.interactivePopGestureRecognizer.delegate = nil;
```

#### 3、UINavigationBar属性
* 导航栏颜色
	```objc
	@property(nullable, nonatomic,strong) UIColor *barTintColor
	```
* 导航栏渲染色
	```objc
	@property(null_resettable, nonatomic,strong) UIColor *tintColor;
	```
* 导航栏背景图片
	```objc
	- (void)setBackgroundImage:(nullable UIImage *)backgroundImage forBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics  UI_APPEARANCE_SELECTOR;
		
	- (nullable UIImage *)backgroundImageForBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics UI_APPEARANCE_SELECTOR;
	```
* 标题属性
	```objc
	@property(nullable,nonatomic,copy) NSDictionary<NSString *,id> *titleTextAttributes
	```

#### 4、修改状态栏颜色
###### 创建一个高20的假状态栏背景
```objc
UIView *statusBarView = [[UIView alloc] initWithFrame:CGRectMake(0, -20, screenWidth, 20)];
statusBarView.backgroundColor=[UIColor blackColor];
```
###### 添加到导航栏上
```objc
[self.navigationController.navigationBar addSubview:statusBarView];
```
###### 设置导航栏的背景图片 
```objc
[self.navigationController.navigationBarsetBackgroundImage:[UIImageimageNamed:@"bg.jpg"] forBarMetrics:UIBarMetricsDefault];
```

## 二、Segue
#### 1、什么是Segue
* Storyboard上每一根用来界面跳转的线，都是一个UIStoryboardSegue对象（简称Segue）

#### 2、Segue的属性
###### 每一个Segue对象，都有3个属性
* 唯一标识	
	```objc
	@property (nonatomic, readonly) NSString *identifier;
	```
* 来源控制器
	```objc
	@property (nonatomic, readonly) id sourceViewController;
	```
* 目标控制器
	```objc
	@property (nonatomic, readonly) id destinationViewController;
	```
		
#### 3、Segue的类型
###### 根据Segue的执行（跳转）时刻，Segue可以分为2大类型
* 自动型：点击某个控件后（比如按钮），自动执行Segue，自动完成界面跳转
	* 按住Control键，直接从控件拖线到目标控制器
	
		**注：如果点击某个控件，不需要做任何判断，直接跳转到下一个界面，建议使用“自动型Segue”** 
* 手动型：需要通过写代码手动执行Segue，才能完成界面跳转
	* 按住Control键，从来源控制器拖线到目标控制器
	* 手动型的Segue需要设置一个标识（如右图）
	* 在需要的时刻，由来源控制器执行perform方法调用对应的Segue
	* [self performSegueWithIdentifier:@"login2contacts" sender:nil];
 	* 如果点击某个控件，需要做一些处理之后才跳转到下一个界面，建议使用“手动型Segue”
 	
#### 4、performSegueWithIdentifier:sender:
* 1、利用performSegueWithIdentifier:方法可以执行某个Segue，跳转界面
	* 完整执行过程如下：
		```objc
		[self performSegueWithIdentifier:@"login2contacts" sender:nil];
		self是来源控制器
		根据identifier去storyboard中找到对应的线，新建UIStoryboardSegue对象
		设置Segue对象的sourceViewController（来源控制器）
		新建并且设置Segue对象的destinationViewController（目标控制器）
		```
* 2、调用sourceViewController的下面方法，做跳转前的准备工作并传入创建好的Segue对象
	```objc
	- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender;
	```
 **提示：sender是调用performSegueWithIdentifier:sender:方法时传入的对象**
* 3、调用Segue对象的- (void)perform;方法开始执行界面跳转操作
	* 如果segue的style是push
		* 取得sourceViewController所在的UINavigationController
		* 调用UINavigationController的push方法将destinationViewController压入栈中，完成跳转
	* 如果segue的style是modal
		* 调用sourceViewController的presentViewController方法将destinationViewController展示出来

## 三、Modal
* 除了push之外，还有另外一种控制器的切换方式，那就是Modal
* 任何控制器都能通过Modal的形式展示出来
* Modal的默认效果：新控制器从屏幕的最底部往上钻，直到盖住之前的控制器为止
* 以Modal的形式展示控制器
	```objc
	- (void)presentViewController:(UIViewController *)viewControllerToPresent animated: (BOOL)flag completion:(void (^)(void))completion
	```
* 关闭当初Modal出来的控制器
	```objc
	- (void)dismissViewControllerAnimated: (BOOL)flag completion: (void (^)(void))completion;
	```
**原则：谁Modal，谁dismiss**

## 四、案例
#### 1、统一设置导航栏样式
```objc
+ (void)initialize
{
    // 当导航栏用在BSNavViewController中, appearance设置才会生效
    //    UINavigationBar *bar = [UINavigationBar appearanceWhenContainedIn:[self class], nil];
    // 设置导航栏背景色和标题
    UINavigationBar *bar = [UINavigationBar appearance];
    [bar setBackgroundImage:[UIImage imageNamed:@"navigationbarBackgroundWhite"] forBarMetrics:UIBarMetricsDefault];
    [bar setTitleTextAttributes:@{NSFontAttributeName : [UIFont boldSystemFontOfSize:20]}];
    
    // 设置导航栏按钮内容格式
    UIBarButtonItem *item = [UIBarButtonItem appearance];
    // UIControlStateNormal
    NSMutableDictionary *attrs = [NSMutableDictionary dictionary];
    attrs[NSFontAttributeName] = [UIFont systemFontOfSize:17];
    attrs[NSForegroundColorAttributeName] = [UIColor blackColor];
    [item setTitleTextAttributes:attrs forState:UIControlStateNormal];
    
    //UIControlStateDisabled
    NSMutableDictionary *disabledAttrs = [NSMutableDictionary dictionary];
    disabledAttrs[NSFontAttributeName] = attrs[NSFontAttributeName];
    disabledAttrs[NSForegroundColorAttributeName] = [UIColor lightGrayColor];
    [item setTitleTextAttributes:disabledAttrs forState:UIControlStateDisabled];
}
```
#### 2、拦截导航栏push方法统一设置子控制器导航栏样式
```objc
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated {
    //判断当前是否push到子控制器(当super写在前面时，count > 1,但此时外界无法修改，因为每当外界修改完时，下面的代码又重新设置了)
    if (self.childViewControllers.count > 0) {
        //设置子控制器的返回键
        viewController.navigationItem.leftBarButtonItem = [UIBarButtonItem itemWithTitle:@"返回" image:@"navigationButtonReturn" highImage:@"navigationButtonReturnClick" target:self action:@selector(pop)];
        //将按钮往左边偏移(设置button的button.imageEdgeInsets和titleEdgeInsets)
        viewController.hidesBottomBarWhenPushed = YES;
    }
    [super pushViewController:viewController animated:YES];
}
```






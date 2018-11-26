---
title: UITabBarController
date: 2016-07-04 10:31:23
tags:
categories: UI
---


## 一、UITabBarController属性/方法
* 子控制器数组
	```objc
	@property(nullable, nonatomic,copy) NSArray<__kindof UIViewController *> *viewControllers
	```
* 当前选中的子控制器
	```objc
	@property(nullable, nonatomic, assign) __kindof UIViewController *selectedViewController;
	```
* 当前选中子控制器的下标
	```objc
	@property(nonatomic) NSUInteger selectedIndex;
	```
	
## 二、UITabBar属性/方法
* item数组
	```objc
	@property(nullable,nonatomic,copy) NSArray<UITabBarItem *> *items;
	```
* 当前选中的item
	```objc
	@property(nullable,nonatomic,assign) UITabBarItem *selectedItem;
	```
* UITabBar颜色
	```objc
	@property(nullable, nonatomic,strong) UIColor *barTintColor
	```
* UITabBar渲染颜色
	```objc
	@property(null_resettable, nonatomic,strong) UIColor *tintColor   UI_APPEARANCE_SELECTOR
	```
	
#### 使用KVC自定义Tabbar
```objc
[self setValue:[[BSTabBar alloc] init] forKey:@"tabBar"];
```
	
## 三、统一设置tabbar样式 
```objc   
+ (void)initialize {	
    [UITabBar appearance].backgroundColor = [UIColor whiteColor];
    [self setTitleAttributes];
}

//统一设置文字样式
+ (void)setTitleAttributes {
    UITabBarItem *item = [UITabBarItem appearance];
    
    NSMutableDictionary *norDict = [NSMutableDictionary dictionary];
    norDict[NSForegroundColorAttributeName] = [UIColor lightGrayColor];
    norDict[NSFontAttributeName] = [UIFont systemFontOfSize:12];
    
    NSMutableDictionary *selDict = [NSMutableDictionary dictionary];
    selDict[NSForegroundColorAttributeName] = [UIColor grayColor];
    selDict[NSFontAttributeName] = norDict[NSFontAttributeName];
    
    [item setTitleTextAttributes:norDict forState:UIControlStateNormal];
    [item setTitleTextAttributes:selDict forState:UIControlStateSelected];
}
```

## 四、TabBar添加控制器一般方法
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    [self addChildVc:[[BSEssenceViewController alloc] init] title:@"精华" image:@"tabBar_essence_icon" selectedImage:@"tabBar_essence_click_icon"];
    
    [self addChildVc:[[BSNewViewController alloc] init] title:@"新帖" image:@"tabBar_new_icon" selectedImage:@"tabBar_new_click_icon"];
    
    [self addChildVc:[[BSFriendTrendsViewController alloc] init] title:@"关注" image:@"tabBar_friendTrends_icon" selectedImage:@"tabBar_friendTrends_click_icon"];
    
    [self addChildVc:[[BSMeViewController alloc] initWithStyle:UITableViewStyleGrouped] title:@"我的" image:@"tabBar_me_icon" selectedImage:@"tabBar_me_click_icon"];
    
    //替换tabBar(KVC)
    [self setValue:[[BSTabBar alloc] init] forKey:@"tabBar"];
}

//添加子控制器
- (void)addChildVc:(UIViewController *)vc title:(NSString *)title image:(NSString *)image selectedImage:(NSString *)selectedImage {
    BSNavViewController *nav = [[BSNavViewController alloc] initWithRootViewController:vc];
    nav.tabBarItem.title = title;
    nav.tabBarItem.image = [UIImage imageNamed:image];
    nav.tabBarItem.selectedImage = [UIImage imageNamed:selectedImage];
    [self addChildViewController:nav];
}
```
---
title: CoreAnimation（核心动画）
date: 2016-07-08 10:31:23
tags:
categories: 提高篇
---

## 一、核心动画(简介)
* Core Animation的动画执行过程都是在后台操作的，不会阻塞主线程。
* **要注意的是，Core Animation是直接作用在CALayer上的，并非UIView。**

#### 1、动画使用步骤
1. 创建动画对象
2. 设置动画属性
3. 把动画对象添加到某个 CALayer 对象上
4. 需要停止动画：可以调用 remove 方法移除动画

#### 2、Core Animation的使用步骤
1. 使用它需要先添加QuartzCore.framework框架和引入主头文件<QuartzCore/QuartzCore.h>(iOS7+不需要)

2. 初始化一个CAAnimation对象，并设置一些动画相关属性

3. 通过调用CALayer的addAnimation:forKey:方法增加CAAnimation对象到CALayer中，这样就能开始执行动画了

4. 通过调用CALayer的removeAnimationForKey:方法可以停止CALayer中的动画

#### 3、注意：（问题）
###### 1、动画太快, 默认时间是0.25秒, 通过 duration 属性修改
###### 2、动画执行完毕以后回到了起始的位置。
* 原因:
 	* 核心动画的本质是在后台移动图层中的内容, 控件本身的 frame 没有发生改变。
 	* 所以看到动画执行完毕后, 又回到了原来的位置
 	* 通过设置动画代理来观察动画执行完毕后控件的 frame 值, layer 的 Frame 值, layer 的 position 值, 都是没有变化的
* 解决:
	 * **解决1：**当动画执行完毕以后, 手动设置控件的位置。在动画的代理方法（动画结束的时候设置控件的 center）
 		
 			self.blueView.center = CGPointMake(300, 50);
 **注意: 不指定 fromValue 的情况下, 如果直接在添加完毕动画后, 设置控件的 center = 最终的终点有问题!!!所以不要在添加完动画以后直接设置 center 为最终的终点, 而要放到代理方法中。**
 	 * **解决2：**
 		* 动画执行完毕后不要删除动画对象
 		* 设置 fillMode
 		```objc
		// 当动画执行完毕后不要删除动画对象
		animation.removedOnCompletion = NO;
		animation.fillMode = kCAFillModeForwards;
		```
		* 缺点： 无法与用户交互, 因为 frame 就没变
* 核心动画的本质：在后台移动图层中的内容,  执行完毕后图层本身的位置并没有发生变化。




#### 4、CAAnimation
###### 1、所有动画对象的父类，负责控制动画的持续时间和速度，是个抽象类，不能直接使用，应该使用它具体的子类
###### 2、属性解析：(红色代表来自CAMediaTiming协议的属性)
* duration：动画的持续时间
* repeatCount：动画的重复次数
* repeatDuration：动画的重复时间
* removedOnCompletion：默认为YES，代表动画执行完毕后就从图层上移除，图形会恢复到动画执行前的状态。如果想让图层保持显示动画执行后的状态，那就设置为NO，不过还要设置fillMode为kCAFillModeForwards
* fillMode：决定当前对象在非active时间段的行为.比如动画开始之前,动画结束之后
* beginTime：可以用来设置动画延迟执行时间，若想延迟2s，就设置为* CACurrentMediaTime()+2，CACurrentMediaTime()为图层的当前时间
* timingFunction：速度控制函数，控制动画运行的节奏
* delegate：动画代理
#### 5、CAPropertyAnimation
* 是CAAnimation的子类，也是个抽象类，要想创建动画对象，应该使用它的两个子类：CABasicAnimation和CAKeyframeAnimation
属性解析：
* keyPath：通过指定CALayer的一个属性名称为keyPath(NSString类型)，并且对CALayer的这个属性的值进行修改，达到相应的动画效果。比如，指定@”position”为keyPath，就修改CALayer的position属性的值，以达到平移的动画效果

## 二、动画
#### 1、CABasicAnimation(基本动画-只有两帧的动画)
* 1、CAPropertyAnimation的子类
* 2、属性解析:
	* fromValue：keyPath相应属性的初始值
	* toValue：keyPath相应属性的结束值
* 3、随着动画的进行，在长度为duration的持续时间内，keyPath相应属性的值从fromValue渐渐地变为toValue

**如果fillMode=kCAFillModeForwards和removedOnComletion=NO，那么在动画执行完毕后，图层会保持显示动画执行后的状态。但在实质上，图层的属性值还是动画执行前的初始值，并没有真正被改变。比如，CALayer的position初始值为(0,0)，CABasicAnimation的fromValue为(10,10)，toValue为(100,100)，虽然动画执行完毕后图层保持在(100,100)这个位置，实质上图层的position还是为(0,0)**

#### 2、CAAnimationGroup
* CAAnimation的子类，可以保存一组动画对象，将CAAnimationGroup对象加入层后，组中所有动画对象可以同时并发运行
属性解析：
* animations：用来保存一组动画对象的NSArray
* 默认情况下，一组动画对象是同时运行的，也可以通过设置动画对象的beginTime属性来更改动画的开始时间

#### 3、CATransition-转场动画
* CAAnimation的子类，用于做转场动画，能够为层提供移出屏幕和移入屏幕的动画效果。iOS比Mac OS X的转场动画效果少一点
* UINavigationController就是通过CATransition实现了将控制器的视图推入屏幕的动画效果
* 属性解析:
	* type：动画过渡类型
	* subtype：动画过渡方向
	* startProgress：动画起点(在整体动画的百分比)
	* endProgress：动画终点(在整体动画的百分比)
	
###### 1、使用UIView动画函数实现转场动画——单视图
```objc
+ (void)transitionWithView:(UIView *)view duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^)(BOOL finished))completion;
```
**参数说明：**
```objc
duration：动画的持续时间
view：需要进行转场动画的视图
options：转场动画的类型
animations：将改变视图属性的代码放在这个block中
completion：动画结束后，会自动调用这个block
```		
###### 2、使用UIView动画函数实现转场动画——双视图
```objc
+ (void)transitionFromView:(UIView *)fromView toView:(UIView *)toView duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options completion:(void (^)(BOOL finished))completion;
```
**参数说明：**
```objc
duration：动画的持续时间
options：转场动画的类型
animations：将改变视图属性的代码放在这个block中
completion：动画结束后，会自动调用这个block
```

## 三、CADisplayLink
* CADisplayLink是一种以屏幕刷新频率触发的时钟机制，每秒钟执行大约60次左右
* CADisplayLink是一个计时器，可以使绘图代码与视图的刷新频率保持同步，而NSTimer无法确保计时器实际被触发的准确时间
* 使用方法：
	* 定义CADisplayLink并制定触发调用方法
	* 将显示链接添加到主运行循环队列
	
#### 4、UIView动画
**常见方法解析:**
```objc
+ (void)setAnimationDelegate:(id)delegate
设置动画代理对象，当动画开始或者结束时会发消息给代理对象
+ (void)setAnimationWillStartSelector:(SEL)selector
当动画即将开始时，执行delegate对象的selector，并且把beginAnimations:context:中传入的参数传进selector
+ (void)setAnimationDidStopSelector:(SEL)selector
当动画结束时，执行delegate对象的selector，并且把beginAnimations:context:中传入的参数传进selector
+ (void)setAnimationDuration:(NSTimeInterval)duration
动画的持续时间，秒为单位
+ (void)setAnimationDelay:(NSTimeInterval)delay
动画延迟delay秒后再开始
+ (void)setAnimationStartDate:(NSDate *)startDate
动画的开始时间，默认为now
+ (void)setAnimationCurve:(UIViewAnimationCurve)curve
动画的节奏控制,具体看下面的”备注”
+ (void)setAnimationRepeatCount:(float)repeatCount
动画的重复次数
+ (void)setAnimationRepeatAutoreverses:(BOOL)repeatAutoreverses
如果设置为YES,代表动画每次重复执行的效果会跟上一次相反
+ (void)setAnimationTransition:(UIViewAnimationTransition)transition forView:(UIView *)view cache:(BOOL)cache
设置视图view的过渡效果, transition指定过渡类型, cache设置YES代表使用视图缓存，性能较好
```

#### 5、UIImageView的帧动画
* UIImageView可以让一系列的图片在特定的时间内按顺序显示
* 相关属性解析:
```objc
animationImages：要显示的图片(一个装着UIImage的NSArray)
animationDuration：完整地显示一次animationImages中的所有图片所需的时间
animationRepeatCount：动画的执行次数(默认为0，代表无限循环)
```
* 相关方法解析:
```objc
- (void)startAnimating; 开始动画
- (void)stopAnimating;  停止动画
- (BOOL)isAnimating;  是否正在运行动画
```

#### 6、UIActivityIndicatorView
* 是一个旋转进度轮，可以用来告知用户有一个操作正在进行中，一般用initWithActivityIndicatorStyle初始化
* 方法解析:
```objc
- (void)startAnimating; 开始动画
- (void)stopAnimating;  停止动画
- (BOOL)isAnimating;  是否正在运行动画
```

* UIActivityIndicatorViewStyle有3个值可供选择：
```objc
UIActivityIndicatorViewStyleWhiteLarge   //大型白色指示器    
UIActivityIndicatorViewStyleWhite      //标准尺寸白色指示器    
UIActivityIndicatorViewStyleGray    //灰色指示器，用于白色背景
```

## 四、参考代码
#### 基本动画: 位移
```objc
// 1. 创建动画对象
CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"position"];
// 默认动画时间是0.25秒
animation.duration = 2.0;
// 设置动画代理
animation.delegate = self;

// 当动画执行完毕后不要删除动画对象
animation.removedOnCompletion = NO;
animation.fillMode = kCAFillModeForwards;

// 2. 设置动画属性值
//animation.fromValue = [NSValue valueWithCGPoint:CGPointMake(200, 30)];
animation.fromValue = [NSValue valueWithCGPoint:self.blueView.center];
animation.toValue = [NSValue valueWithCGPoint:CGPointMake(240, 500)];
// 3. 将动画添加到对应的layer 中
// 这样每次会添加一个新的动画
 //self.blueView.layer addAnimation:animation forKey:nil]
 // 注意： 如果下面的 key 指定了一个写死的 key,@"animation100"，这样不会每次都添加一个新的动画了。
[self.blueView.layer addAnimation:animation forKey:@"animation1"];
// 动画执行完毕后设置控件的 center
//self.blueView.center = CGPointMake(300, 50);
```

#### 2、基本动画: 缩放动画
```objc
// 创建动画对象
CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
// 设置动画属性
anim.fromValue = @(1.0f);
anim.toValue = @(0.7f);
// 设置重复次数
anim.repeatCount = 10;

// 将动画对象添加到 layer 中
[self.blueView.layer addAnimation:anim forKey:nil];
```

#### 3、基本动画: 旋转动画
```objc
CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"transform.rotation"];

anim.duration = 3;
anim.repeatCount = CGFLOAT_MAX;
// 不要每次都设置起始度数
//anim.fromValue = @(0);
anim.toValue = @(M_PI * 2);

[self.blueView.layer addAnimation:anim forKey:nil];
```
###### 一些问题：
**1、注意: 如果 [self.blueView.layer addAnimation:anim forKey:nil];没有指定 forKey, 那么每次都会添加一个新动画, 会越来越快**

###### 解决办法
```objc
// 判断如果已经有动画对象了, 就不再添加了
if ([self.blueView.layer animationForKey:@"anim1"] != nil) {
return;
}
```
**2、注意: 如果当动画正在执行的时候, 将程序退出到后台, 那么当程序再次进入前台的时候就不执行了。**

###### 解决办法
* 原因: 因为再次进入前台后动画已经被删除了。
* 解决1: anim.removedOnCompletion = NO;

**3、问题: 当双击 home 键的时候, 动画不会暂停。**

###### 解决办法
```objc
// 暂停
- (void)applicationWillResignActive:(UIApplication *)application {
ViewController *vc =  (ViewController *)self.window.rootViewController;
[vc pause];
}

// 恢复
- (void)applicationDidBecomeActive:(UIApplication *)application {
ViewController *vc =  (ViewController *)self.window.rootViewController;
[vc resume];
}
```
#### 4、基本动画: 关键帧动画
###### 1、设置 values 属性
```objc
CAKeyframeAnimation *anim = [CAKeyframeAnimation animationWithKeyPath:@"position"];
anim.duration = 3;
anim.removedOnCompletion = NO;
CGPoint p1 = CGPointMake(10, 10);
CGPoint p2 = CGPointMake(10, 110);
CGPoint p3 = CGPointMake(110, 110);
CGPoint p4 = CGPointMake(110, 10);
CGPoint p5 = CGPointMake(10, 10);
anim.values = @[[NSValue valueWithCGPoint:p1], [NSValue valueWithCGPoint:p2], [NSValue valueWithCGPoint:p3], [NSValue valueWithCGPoint:p4],[NSValue valueWithCGPoint:p5]];

[self.blueView.layer addAnimation:anim forKey:nil];
```
###### 2、设置 path 属性
```objc
CAKeyframeAnimation *anim = [CAKeyframeAnimation animationWithKeyPath:@"position"];
anim.duration = 3;
anim.removedOnCompletion = NO;
anim.fillMode = kCAFillModeForwards;
UIBezierPath *path = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(50, 50, 150, 150)];
anim.path = path.CGPath;

[self.blueView.layer addAnimation:anim forKey:nil];
```
###### 3、 模拟"app 抖动"
**思路: 通过设置左右旋转实现**
```objc
if ([self.blueView.layer animationForKey:@"shake"]) {
return;
}

CAKeyframeAnimation *anim = [CAKeyframeAnimation animationWithKeyPath:@"transform.rotation"];
anim.values = @[@(-M_PI / 36), @(M_PI / 36), @(-M_PI / 36)];
anim.duration = 0.15;
anim.repeatCount = CGFLOAT_MAX;
[self.blueView.layer addAnimation:anim forKey:@"shake"];
```

#### 5、组动画
```objc
// 动画组, 组动画
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
UITouch *touch = touches.anyObject;
CGPoint location = [touch locationInView:touch.view];


CAAnimationGroup *groupAnim = [[CAAnimationGroup alloc] init];

// 位移
CABasicAnimation *anim1 = [CABasicAnimation animationWithKeyPath:@"position"];
anim1.toValue = [NSValue valueWithCGPoint:location];

// 缩放
CABasicAnimation *anim2 = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
anim2.toValue = @(0.3);


// 旋转
CABasicAnimation *anim3 = [CABasicAnimation animationWithKeyPath:@"transform.rotation"];
anim3.toValue = @(M_PI * 8);

// 关键帧动画
CAKeyframeAnimation *anim4 = [CAKeyframeAnimation animationWithKeyPath:@"position"];
UIBezierPath *path = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(180, 150, 150, 150)];
anim4.path = path.CGPath;

groupAnim.animations = @[anim1, anim2, anim3, anim4];
groupAnim.duration = 1.0;
groupAnim.repeatCount = CGFLOAT_MAX;

[self.blueView.layer addAnimation:groupAnim forKey:nil];
```





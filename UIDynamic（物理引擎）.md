---
title: UIDynamic（物理引擎）
date: 2016-07-05 10:31:23
tags:
categories: 提高篇
---


## 一、介绍
#### 1、相关概念a. 谁要进行物理仿真?(要谁做)	* 物理仿真元素(Dynamic Item)	* 是任何遵守了UIDynamicItem协议的对象b. 执行怎样的物理仿真效果?怎样的动画效果?(做什么动画)	* 物理仿真行为(Dynamic Behavior) 
	* 仿真行为,是动力学行为的父类,基本的动力学行为类
	UIGravityBehavior、UICollisionBehavior、UIAttachmentBehavior、UISnapBehavi or、UIPushBehavior以及UIDynamicItemBehavior均继承自该父类,可以组合使用c. 让物理仿真元素执行具体的物理仿真行为(开始做) 
	* 物理仿真器(Dynamic Animator)	* 为动力学元素提供物理学相关的能力及动画,同时为这些元素提供相关的上 
	* 下文,是动力学元素与底层iOS物理引擎之间的中介,将Behavior对象添加到 Animator即可实现动力仿真d. 注意不是任何对象都可以做物理仿真效果 
	* 物理仿真元素要素:	* 任何遵守了UIDynamicItem协议的对象 UIView默认已经遵守了	* UIDynamicItem协议,因此任何UI控件都能做物理仿真 	* UICollectionViewLayoutAttributes类默认也遵守UIDynamicItem协议
	
**仿真器使用时必须设置一个strong属性使其不会立即销毁**
```objc	
@property (nonatomic, strong) UIDynamicAnimator *animator;
```	
#### 2、使用步骤
a. 创建一个UIDynamicAnimator对象b. 创建行为对象(UIDynamicBehavior)c. 将要执行动画的对象添加到UIDynamicBehavior中一般会将 UIView 添加到行为对象中 UIView 遵守了UIDynamicItem协议

## 二、物理仿真行为
#### 1、重力行为(Gravity)
###### 1、重力行为用于给动力学元素指定一个重力向量
###### 2、代码示例
```objc
//1 创建物理仿真器UIDynamicAnimator *animator = [[UIDynamicAnimator alloc]initWithReferenceView:self.redView];self.animator = animator;//2 创建重力行为(物理行为)UIGravityBehavior *behavior = [[UIGravityBehavior alloc]initWithItems:@[self.redView]]; //量级(用来控制加速度,1.0代表加速度是1000 points/second2) behavior.magnitude = 0.2; //方向//behavior.gravityDirection = CGVectorMake(1, 1);behavior.angle = 0;//3 把物理行为添加到物理仿真器中 开始动画[animator addBehavior:behavior];
```
#### 2、碰撞行为(Collision)
###### 1、相关属性translatesReferenceBoundsIntoBoundary
```objctranslatesReferenceBoundsIntoBoundary设置为YES而不是明确的添加边界的坐标。这样会使这个边界使用 UIDynamicAnimator 提供的参考系的边界。
```collisionMode
```objc	UICollisionBehaviorModeItems 碰到元素碰撞,边界不碰撞 UICollisionBehaviorModeBoundaries 碰到边界碰撞,元素不碰撞 UICollisionBehaviorModeEverything 默认,碰到边界或元素会发生碰撞
```
###### 2、代码示例
```objc
//1 创建物理仿真器,动画的范围self.animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];//2 创建物理仿真行为//2.1 重力行为UIGravityBehavior *gravity = [[UIGravityBehavior alloc] initWithItems:@[self.redView]];//2.2 碰撞行为UICollisionBehavior *collision = [[UICollisionBehavior alloc] initWithItems:@[self.redView]];//2.3 碰撞的边界,translatesReferenceBoundsIntoBoundary设置为YES而不是明确的添加边界的坐标。这样会使这个边界使用 UIDynamicAnimator 提供的参考系的边界。 collision.translatesReferenceBoundsIntoBoundary = YES;//3 把物理仿真行为添加到物理仿真器中 [self.animator addBehavior:gravity]; [self.animator addBehavior:collision];
```
###### 3、碰撞行为-其它
1. 两种自定义边界的方式,设置直线 
	a. 添加边界,设置两个点
	```objc	[collision addBoundaryWithIdentifier:@"b1" fromPoint:CGPointMake(0, 200) toPoint:CGPointMake(180, 250)];
	```	b. 使用路径的方式	```objc	UIBezierPath *path = [UIBezierPath bezierPath];	[path moveToPoint:CGPointMake(0, 200)];	[path addLineToPoint:CGPointMake(180, 250)];	[collision addBoundaryWithIdentifier:@"b1" forPath:path];
	```2. 自定义边界,设置矩形的边界
```objcUIBezierPath *path = [UIBezierPath bezierPathWithRect:CGRectMake(0, 200, 180, 20)]; [collision addBoundaryWithIdentifier:@"b2" forPath:path];
```3. 碰撞过程中监听frame的变化 
```objc[collision setAction:^{NSLog(@"%@",NSStringFromCGRect(self.redView.frame)); }];
```4. 碰撞行为的弹力系数(0-1之间)
```objcUIDynamicItemBehavior *item = [[UIDynamicItemBehavior alloc] initWithItems: @[self.redView]];item.elasticity = 1;
```添加到物理仿真器中```objc		
[self.animator addBehavior:item];
```5. 设置碰撞行为的代理,碰撞到边界以后改变物体的状态
```objc- (void)collisionBehavior:(UICollisionBehavior *)behavior beganContactForItem:(id<UIDynamicItem>)item withBoundaryIdentifier:(id<NSCopying>)identifier atPoint:(CGPoint)p {//开始的碰撞物体UIView *view = (UIView *)item;//边界的idNSString *strId = (NSString *)identifier; //当碰撞到黄色边界以后改变当前物体的颜色 if ([strId isEqualToString:@"b2"]) {[UIView animateWithDuration:0.3 animations:^{ view.backgroundColor = [UIColor blackColor];} completion:^(BOOL finished) {view.backgroundColor = [UIColor redColor]; }];} }
```

#### 3、捕捉行为(Snap)
###### 代码示例
```objc
//1 物理仿真器self.animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];//2 创建捕捉行为UISnapBehavior *snap = [[UISnapBehavior alloc] initWithItem:self.redView snapToPoint:location]; //阻尼,减幅,衰减 取值(0-1)snap.damping = 0;//3 把行为添加到物理仿真器[self.animator addBehavior:snap];
```

#### 4、附着行为(Attachment)
**附着行为描述一个视图与一个锚点或者另一个视图相连接的情况附着 行为描述的是两点之间的连接情况,可以模拟刚性或者弹性连接在多 个物体间设定多个UIAttachmentBehavior,可以模拟多物体连接**

###### 1、属性
* attachedBehaviorType:连接类型(连接到锚点或视图) items:连接视图数组* anchorPoint:连接锚点* length:距离连接锚点的距离 只要设置了以下两个属性,即为弹性连接 * damping:振幅大小* frequency:振动频率

###### 2、示例代码
```objc
//1 物理仿真器self.animator = [[UIDynamicAnimator alloc]initWithReferenceView:self.view];//2 重力行为UIGravityBehavior *gravity = [[UIGravityBehavior alloc] initWithItems:@[self.redView]]; [gravity setAction:^{view.startPoint = self.redView.center;view.endPoint = point; }];//2.1 添加附着行为UIAttachmentBehavior *attachment = [[UIAttachmentBehavior alloc] initWithItem:self.redView attachedToAnchor:point];//弹性行为attachment.frequency = 0.5; attachment.damping = 0.5;//3 把行为添加到仿真器[self.animator addBehavior:gravity]; [self.animator addBehavior:attachment];
```

#### 5、推动行为(Push)
**推行为可以为一个视图施加一个作用力,该力可以是持续的,也可以是一次性的可以设置力的大小,方向和作用点等信息**

###### 1、属性
* mode:推动类型(一次性或是持续推) 
* angle:推动角度* magnitude:推动力量

###### 2、示例代码
```objc
//1 物理仿真器self.animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view]; //2 一次性推动行为(持续性推动行为)UIPushBehavior *push = [[UIPushBehavior alloc] initWithItems:@[self.redView]mode:UIPushBehaviorModeContinuous];////设置推动的方向和推力的大小 push.angle = M_PI_2; push.magnitude = 1; //设置向量push.pushDirection = CGVectorMake(0, 100); //3 把推动行为添加都物理仿真器[self.animator addBehavior:push];
```	
#### 6、UIDynamicItemBehavior
**UIDynamicItemBehavior:元素行为**
###### 1、属性
* DynamicItem是一个辅助的行为,用来设置运动学元素参与物理仿真过程中的参数,如:弹 性系数、摩擦系数、密度、阻力、角阻力以及是否允许旋转等* elasticity(弹性系数):决定了碰撞的弹性程度,比如碰撞时物体的弹性 * friction(摩擦系数) :决定了沿接触面滑动时的摩擦力大小* density(密度): 跟size结合使用,计算物体的总质量。质量越大,物体加速或减速就越 困难* resistance(阻力):决定线性移动的阻力大小,与摩擦系数不同,摩擦系数只作用于滑动 运动* angularResistance(角阻力) :决定旋转运动时的阻力大小* allowsRotation(允许旋转):这个属性很有意思,它在真实的物理世界没有对应的模型。 设置这个属性为 NO 物体就完全不会转动,而无论施加多大的转动力
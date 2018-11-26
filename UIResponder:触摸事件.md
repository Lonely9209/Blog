---
title: UIResponder/触摸事件
date: 2016-07-03 10:31:23
tags:
categories: UI
---

## 一、响应者对象UIResponder
* 在iOS中不是任何对象都能处理事件，只有继承了UIResponder的对象才能接收并处理事件。我们称之为“响应者对象”
* UIApplication、UIViewController、UIView都继承自UIResponder，因此它们都是响应者对象，都能够接收并处理事件

#### UIResponder的成员
###### UIResponder内部提供了以下方法来处理事件
* 1、触摸事件

```objc
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;
```
* 2、加速计事件

```objc
- (void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event;
- (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event;
- (void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event;
```

* 3、远程控制事件

```objc
- (void)remoteControlReceivedWithEvent:(UIEvent *)event;
```
	
## 二、触摸事件	
#### 1、UIView的触摸事件处理
**UIView是UIResponder的子类，可以覆盖下列4个方法处理不同的触摸事件**
* 一根或者多根手指开始触摸view
```objc
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
```
		
* 一根或者多根手指在view上移动，系统会自动调用view的下面方法（随着手指的移动，会持续调用该方法）
	```objc
	- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event
	```
* 一根或者多根手指离开view，系统会自动调用view的下面方法
	```objc
	- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event
	```
* 触摸结束前，某个系统事件(例如电话呼入)会打断触摸过程，系统会自动调用view的下面方法
	```objc
	- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
	```
**touches中存放的都是UITouch对象**

#### 2、UITouch
* 当用户用一根手指触摸屏幕时，会创建一个与手指相关联的UITouch对象
* 一根手指对应一个UITouch对象
* UITouch的作用
	* 保存着跟本次手指触摸相关的信息，比如触摸的位置、时间、阶段
	* 当手指移动时，系统会更新同一个UITouch对象，使之能够一直保存该手指在的触摸位置
	* 当手指离开屏幕时，系统会销毁相应的UITouch对象
**提示：iPhone开发中，要避免使用双击事件！**	

#### 3、UITouch的属性
* 触摸产生时所处的窗口
	```objc
	@property(nonatomic,readonly,retain) UIWindow    *window;
	```
* 触摸产生时所处的视图
	```objc
	@property(nonatomic,readonly,retain) UIView      *view;
	```
* 短时间内点按屏幕的次数，可以根据tapCount判断单击、双击或更多的点击
	```objc
	@property(nonatomic,readonly) NSUInteger          tapCount;
	```
* 记录了触摸事件产生或变化时的时间，单位是秒

	```objc
	@property(nonatomic,readonly) NSTimeInterval      timestamp;
	```
* 当前触摸事件所处的状态
	```objc
	@property(nonatomic,readonly) UITouchPhase        phase;
	```	
#### 4、UITouch的方法
* 1、- (CGPoint)locationInView:(UIView *)view;
* 返回值表示触摸在view上的位置
* 这里返回的位置是针对view的坐标系的（以view的左上角为原点(0, 0)）
* 调用时传入的view参数为nil的话，返回的是触摸点在UIWindow的位置
* 2、- (CGPoint)previousLocationInView:(UIView *)view;
* 该方法记录了前一个触摸点的位置

#### 5、UIEvent
* 每产生一个事件，就会产生一个UIEvent对象
* UIEvent：称为事件对象，记录事件产生的时刻和类型
* 常见属性
* 事件类型
	```objc
	@property(nonatomic,readonly) UIEventType     type;
	@property(nonatomic,readonly) UIEventSubtype  subtype;
	```
* 事件产生的时间
	```objc
	@property(nonatomic,readonly) NSTimeInterval  timestamp;
	```
**UIEvent还提供了相应的方法可以获得在某个view上面的触摸对象（UITouch）**
#### 6、事件传递
* hitTest:方法介绍
	* pointInside:withEvent:方法介绍:
	* 在 hitTest 方法内部调用 pointInside:withEvent:方法来查找给定的点在不在当前的控件范围之内。
* 触摸事件：touchesXxxxx

## 三、介绍控件不能接受用户交互的情况
* 演示要求:在控制器 view 中创建多个 UIView, 并且为每个 UIView 指定一个自定义 View, 实现 touchesBegan 方法.
* 1、查看默认情况下 touchesBegan: 事件的触发顺序
* 2、控件的userInteractionEnabled = NO 的情况下不能与用户交互 **注意: 如果父容器不能与用户交互, 那么在该容器中的所有子控件也不能与用户交互（例如: 添加在 UIImageView 中的按钮）**
* 3、透明度小于等于0.01, alpha = 0.01
* 4、控件被隐藏的时候, hidden = YES
* 5、如果子视图的位置超出了父视图的有效范围, 那么子视图也是无法与用户交互的, 即使设置了父视图的 clipsToBounds = NO, 可以看到, 但是也是无法与用户交互的
* 6、默认情况下, 从控件库中拖拽的 UIImageView 是无法接受用户的触摸事件的
* ** 演示向 UIImageView 中添加一个按钮, 监听按钮的点击事件。**
* **UIImageView 默认是不支持多点触摸, 也不响应用户事件的。**
* 补充: 直接从媒体库中把图片拖拽进来（通过这种方式拖进来的UIImageView, 默认即支持多点触摸也支持用户交互, 并且图片框大小就是图片的实际大小）

## 四、响应者链事件处理过程
#### 如果hit-test视图无法处理事件，则通过响应者链向上传递
* 1、如果hit-test视图的控制器存在，就传递给控制器；如果控制器不存在，则将其传递给它的父视图
* 2、如果视图或它的控制器无法处理收到的事件或消息，则将其传递给该视图的父视图
* 3、每一个在视图继承树中的上层视图如果不能处理收到的事件或消息，则重复上面的步骤1，2
* 4、在视图层次结构的最顶级视图，如果也不能处理收到的事件或消息，则其将事件或消息传递给window对象进行处理
* 5、如果window对象也不处理，则其将事件或消息传递给UIApplication对象
* 6、如果UIApplication也不能处理该事件或消息，则将其丢弃

## 五、响应者链的事件传递过程
* 如果view的控制器存在，就传递给控制器；如果控制器不存在，则将其传递给它的父视图
* 在视图层次结构的最顶级视图，如果也不能处理收到的事件或消息，则其将事件或消息传递给window对象进行处理
* 如果window对象也不处理，则其将事件或消息传递给UIApplication对象
* 如果UIApplication也不能处理该事件或消息，则将其丢弃
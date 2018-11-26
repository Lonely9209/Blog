---
title: CALayer
date: 2016-07-06 10:31:23
tags:
categories: 提高篇
---

## 一、简介
* 在iOS中，你能看得见摸得着的东西基本上都是UIView，比如一个按钮、一个文本标签、一个文本输入框、一个图标等等，这些都是UIView，**其实UIView之所以能显示在屏幕上，完全是因为它内部的一个图层**

* 在创建UIView对象时，UIView内部会自动创建一个图层(即CALayer对象)，通过UIView的layer属性可以访问这个层
```objc
@property(nonatomic,readonly,retain) CALayer *layer; 
```
* 当UIView需要显示到屏幕上时，会调用drawRect:方法进行绘图，并且会将所有内容绘制在自己的图层上(layer)，绘图完毕后，系统会将图层拷贝到屏幕上，于是就完成了UIView的显示

*换句话说，UIView本身不具备显示的功能，是它内部的层才有显示功能
## 二、CALayer应用
#### 1、CALayer的属性
```objc
@property CGFloat borderWidth;				//边框宽度
@property CGColorRef borderColor;			//边框颜色(CGColorRef类型)
@property CGColorRef backgroundColor; 		//背景颜色
@property float opacity;					//透明度
@property CGColorRef shadowColor; 			//阴影颜色
@property float shadowOpacity;
@property CGSize shadowOffset;
@property CGFloat shadowRadius;
@property(strong) id contents;				内容(比如设置为图片CGImageRef)
@property CGFloat cornerRadius;				//圆角半径
@property CGRect bounds;					//宽度和高度
@property CGPoint position;					//位置(默认指中点，具体由anchorPoint决定)
@property CGPoint anchorPoint;				//锚点(x,y的范围都是0-1)，决定了position的含义
@property CATransform3D transform;			//形变属性
@property(getter=isHidden) BOOL hidden;
@property(readonly) CALayer *superlayer;
@property(copy) NSArray *sublayers;
@property BOOL masksToBounds;
```
#### 2、UIView和CALayer的选择
* 通过CALayer，就能做出跟UIImageView一样的界面效果

* 既然CALayer和UIView都能实现相同的显示效果，那究竟该选择谁好呢？
	* 其实，对比CALayer，UIView多了一个事件处理的功能。也就是说，CALayer不能处理用户的触摸事件，而UIView可以
	* 所以，如果显示出来的东西需要跟用户进行交互的话，用UIView；如果不需要跟用户进行交互，用UIView或者CALayer都可以
当然，CALayer的性能会高一些，因为它少了事件处理的功能，更加轻量级

	**UIView ： 接受和处理系统事件、触摸事件。**
	
	**CALayer ： 显示内容**
	
#### 3、position和anchorPoint
###### 1、@property CGPoint position;
* 用来设置CALayer在父层中的位置
* 以父层的左上角为原点(0, 0)

###### 2、@property CGPoint anchorPoint;
* 称为“定位点”、“锚点”
* 决定着CALayer的position属性所指的是哪个点
* 以自己的左上角为原点(0, 0)
* 它的x、y取值范围都是0~1，默认值为（0.5, 0.5）

#### 4、隐式动画
* 每一个UIView内部都默认关联着一个CALayer, 我们可称这个Layer为Root Layer（根层）
* 所有的非Root Layer, 也就是手动创建的CALayer对象, 都存在着隐式动画。 root layer 是没有隐式动画的
* **可以通过动画事务(CATransaction)关闭默认的隐式动画效果**
```objc
[CATransaction begin];
[CATransaction setDisableActions:YES];
self.myview.layer.position = CGPointMake(10, 10);
[CATransaction commit];
```
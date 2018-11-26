---
title: Quartz2D(绘图)
date: 2016-07-07 10:31:23
tags:
categories: 提高篇
---


## 一、基本图形绘制
* 线段（线宽、线段样式）
* 矩形（空心、实心、颜色）
* 三角形、四边形等形状
* 1、说明 - (void)drawRect:(CGRect)rect 什么时候调用、调用次数等
	 * 当 view 第一次被显示的时候调用(调用一次)
	 * 或者是重绘事件被触发的时候
	 * 不要手动去调用这个方法
- **手动调用重绘方法 setNeedsDisplay 或者 setNeedsDisplayInRect:**
```objc
setNeedsDisplay 方法会把之前的内容都清除掉, 然后再重绘。
setNeedsDisplayInRect:<#(CGRect)#>局部刷新
```
* 2、说明为什么要在 - (void)drawRect:(CGRect)rect 方法中进行绘图
* **只有在这个方法中才能获取当前 View 的绘图上下文**

#### 说明
 1. 当要向UIView上绘图的时候, 必须重写UIView的drawRect:方法, 然后在这个方法中进行绘图
 2. 在drawRect:方法中获取的参数rect, 指的就是当前view的bounds属性
 3. 为什么向当前view中绘制图形, 必须在drawRect:方法中进行?
 	* 3.1 原因: 只有在当前view的drawRect:方法中才能成功的获取当前view的"图形上下文", 有了图形上下文才能进行绘图
 	* 3.2 为什么只有在drawRect:方法中才能获取当前view的图形上下文呢？
 	* 3.3 原因: 是因为系统在调用drawRect:方法之前已经帮我们创建好了一个与当前view相关的图形上下文了, 然后才调用的drawRect:方法, 所以在drawRect:方法中, 我们就可以成功获取当前view的图形上下文了。
 	* 3.4 如何创建一个图形上下文？（后面说）
  	* 3.5 drawRect:方法是谁来调用的？什么时候调用的？
 		* 3.5.1 drawRect:方法是系统帮我们调用的, 千万别手动去调用这个方法。原因是, 自己手动去调用drawRect:方法的时候无法保证系统已经帮我们创建好了"图形上下文", 所以这样就无法保证在drawRect:方法中获取"图形上下文"对象, 所以也就无法绘图。
 		* 3.5.2 drawRect:方法的什么时候被调用？
          * 1> 当这个View第一次显示的时候会条用一次drawRect:方法。
          * 2> 当这个View执行重绘操作的时候, 会重新调用drawRect:方法来进行绘图。
          * 3> 通过调用【[self setNeedsDisplay];// 把这个view都重绘一次】 或 【[self setNeedsDisplayInRect:(CGRect)]// 把view中的某个区域重绘一次】 来实现重绘
          * 4> 在每次调用 setNeedsDisplay 或 setNeedsDisplayInRect:方法的时候, 内部会先创建一个与当前view相关连的"图形上下文"然后再调用drawRect:实现重绘。
          
**注意: UIBezierPath 对象可以独立使用,  无需手动获取“图形上下文”对象，此处为了更好的理解“图形上下文对象”所以暂时还是采用手动获取“图形上下文”对象的方式来绘图
**        
#### 参考代码
###### 1、绘制一个"三角形"
```objc
// 1. 获取当前的图形上下文
CGContextRef ctx = UIGraphicsGetCurrentContext();
// 2. 在上下文中绘制图形（拼接路径）
// 2.1 设置一个起点
CGContextMoveToPoint(ctx, 20, 20);
// 2.2 添加一条直线到(100, 100)这个点
CGContextAddLineToPoint(ctx, 100, 100);
// 2.3 再添加一条线
CGContextAddLineToPoint(ctx, 120, 30);
// 2.4 再添加一条线段
//CGContextAddLineToPoint(ctx, 20, 20);
// 另外一种做法: 直接关闭路径（连接最后一个点和起点）
CGContextClosePath(ctx);
// 3. 把上下文渲染显示到 HMView01上
// StrokePath 表示把路径以空心的形式渲染出来。
CGContextStrokePath(ctx);
```
###### 2、绘制一个实心"矩形"
```objc
// 绘制一个"四边形"
// 1. 获取当前图形上下文
CGContextRef ctx = UIGraphicsGetCurrentContext();
// 2. 开始绘制路径
CGContextAddRect(ctx, CGRectMake(20, 20, 100, 120));
// 3. 渲染
CGContextFillPath(ctx);
```
###### 3、设置图形的颜色
```objc
// 绘制一个"四边形"
// 1. 获取当前图形上下文
CGContextRef ctx = UIGraphicsGetCurrentContext();
// 2. 开始绘制路径
CGContextAddRect(ctx, CGRectMake(20, 20, 100, 120));

//============ C 语言的方式设置颜色 =================
CGContextSetRGBFillColor(ctx, 200/255.0, 100/255.0, 50/255.0, 1.0);
//CGContextSetRGBStrokeColor(<#CGContextRef context#>, <#CGFloat red#>, <#CGFloat green#>, <#CGFloat blue#>, <#CGFloat alpha#>)
//============ C 语言的方式设置颜色 =================

//============ OC 的方式设置颜色 =================
// 设置空心图形的线条颜色
// [[UIColor redColor] setStroke];
// 设置实心图形的填充颜色
// [[UIColor redColor] setFill];
// 统一设置"空心图形" 和 "实心图形"的颜色
//[[UIColor redColor] set];
//============ OC 的方式设置颜色 =================
// 3. 渲染
CGContextFillPath(ctx);
```

###### 4、设置不同线段, 不同颜色
```objc
//画两根线, 一根红色, 一根蓝色
// 1. 获取上下文对象
CGContextRef ctx = UIGraphicsGetCurrentContext();
// 2. 绘制图形
// 2.1 设置起点
CGContextMoveToPoint(ctx, 50, 50);
// 2.2 添加一根线
CGContextAddLineToPoint(ctx, 50, 150);
// 2.3 设置线段颜色
[[UIColor redColor] set];
// 2.4 设置线宽
CGContextSetLineWidth(ctx, 10);
// 渲染一次
CGContextStrokePath(ctx);

// 再移动到一个新的起点
CGContextMoveToPoint(ctx, 100, 50);
// 再添加一根线
CGContextAddLineToPoint(ctx, 100, 150);
// 设置线的颜色
[[UIColor blueColor] set];
// 3. 渲染"上下文对象"到 view 上
CGContextStrokePath(ctx);
```

###### 5、设置线段"头尾部"的样式
```objc
 // 1. 获取上下文对象
 CGContextRef ctx = UIGraphicsGetCurrentContext();
 // 2. 绘制路径
 // 2.1 移动到起点
 CGContextMoveToPoint(ctx, 20, 20);
 // 2.2 添加一条线段
 CGContextAddLineToPoint(ctx, 100, 100);
 // 2.3 设置颜色
 [[UIColor redColor] set];
 // 2.4 设置线段宽度
 CGContextSetLineWidth(ctx, 15);
 // 2.5 设置线段头尾部样式
//enum CGLineCap {
//kCGLineCapButt, 默认值
//kCGLineCapRound, 圆角
//kCGLineCapSquare 方角
//};
CGContextSetLineCap(ctx, kCGLineCapSquare);
// 3. 渲染
CGContextStrokePath(ctx);
```

###### 6、设置转折点样式
```objc
 // 2.4 设置线段宽度
 CGContextSetLineWidth(ctx, 15);
 // 2.5 设置线段头尾部样式
 CGContextSetLineCap(ctx, kCGLineCapRound);
 // 2.6 设置转折点样式
 CGContextSetLineJoin(ctx, kCGLineJoinBevel);
 ```
###### 7、文字绘制(通过OC 来绘制图形)
**1、绘制一段文字(思路: 直接使用 OC 的方法, 无需手动获取上下文对象)**
```objc
NSString *str = @"哈哈, 黑马程序员 iOS学院。";
NSDictionary *attrs = @{
    NSForegroundColorAttributeName : [UIColor redColor],
    NSFontAttributeName : [UIFont systemFontOfSize:20]
};
[str drawAtPoint:CGPointMake(30, 50) withAttributes:attrs];
```
**2、绘制一段文字到一个指定的区域**

思路: 调用字符串的 drawInRect: 方法.
```objc
// 1. 绘制文字
NSString *str = @"哈哈, 黑马程序员 iOS。";
NSDictionary *attrs = @{
NSForegroundColorAttributeName : [UIColor redColor],
NSFontAttributeName : [UIFont systemFontOfSize:20]
};

[str drawInRect:CGRectMake(20, 20, 50, 250) withAttributes:attrs];
// 1. 获取上下文
CGContextRef ctx = UIGraphicsGetCurrentContext();
// 2. 把矩形画出来
CGContextAddRect(ctx,CGRectMake(20, 20, 50, 250));
// 3. 渲染
CGContextStrokePath(ctx);
```
###### 8、图片绘制(通过OC 来绘制图形)
**1、绘制一张图片到 UIView 上**
```objc
// 1. 获取图片
UIImage *img = [UIImage imageNamed:@"dst2"];
// 2. 把图片画到当前的 view中
[img drawAtPoint:CGPointMake(0, 0)];
```
**2、在指定的矩形中绘制图片（会自动拉伸）**
```objc
// 1. 获取图片
UIImage *img = [UIImage imageNamed:@"dst2"];
// 2. 把图片画到当前的 view中
[img drawInRect:CGRectMake(0, 0, 150, 150)];
```
**3、画格子花纹效果(（pattern）), 思路: 调用drawAsPatternInRect:方法**
```objc
// 1. 获取图片
UIImage *img = [UIImage imageNamed:@"abc"];
// 2. 把图片画到当前的 view中
[img drawAsPatternInRect:self.bounds];
// 3. 在右下角显示文字
NSString *str = @"@Lonely ios";
NSDictionary *attrs = @{
NSForegroundColorAttributeName : [UIColor redColor],
NSFontAttributeName : [UIFont systemFontOfSize:20]
};
[ str drawInRect:CGRectMake(0, 100, 200, 30) withAttributes:attrs];
```

## 二、图形上下文栈

* **CGContextSaveGState(ctx);// 保存ctx这个绘图上下文对象**
* **CGContextRestoreGState(ctx); // 恢复 ctx这个图形上下文**

#### 参考代码
###### 1、矩阵操作
```objc
// 1. 获取上下文
CGContextRef ctx = UIGraphicsGetCurrentContext();
// 保存上下文
CGContextSaveGState(ctx);
//======================= 矩阵操作 ============================
// 1.1 旋转
CGContextRotateCTM(ctx, M_PI_4 * 0.5);
// 1.2 缩放
CGContextScaleCTM(ctx, 0.5, 0.5);
//======================= 矩阵操作 ============================
// 2. 绘制一些图形
CGContextMoveToPoint(ctx, 10, 10);
CGContextAddLineToPoint(ctx, 100, 100);
CGContextAddEllipseInRect(ctx, CGRectMake(130, 150, 100, 100));
// 恢复上下文
CGContextRestoreGState(ctx);
CGContextAddRect(ctx, CGRectMake(70, 90, 100, 80));
// 3. 渲染
CGContextStrokePath(ctx);
```
**注：在绘制任何图形前保存上下文, 在绘制最后一个图形前恢复上下文**

###### 2、图片裁剪
```objc
CGContextRef ctx = UIGraphicsGetCurrentContext();
// 1. 画圆，这里最好使用 Ellipse 来绘制圆（画圆和画图片都从0,0点开始）。
CGContextAddArc(ctx, 100, 100, 90, 0, M_PI * 2, 1);
// 2. 裁剪上下文, 注意裁剪完毕就只能在裁剪好的区域内画东西了, 超出的地方无法绘制图形。
CGContextClip(ctx);
// 3. 把图片绘制上去
UIImage *img = [UIImage imageNamed:@"dst2"];
[img drawAtPoint:CGPointMake(0, 0)];
```
###### 3、图片裁剪（返回圆形边框View）
```objc
- (UIImage *)zc_circleImage
{
//NO -> 透明
UIGraphicsBeginImageContextWithOptions(self.size, NO, 0.0);
//获取上下文
CGContextRef contex = UIGraphicsGetCurrentContext();
//添加圆
CGRect rect = CGRectMake(0, 0, self.size.width, self.size.height);
CGContextAddEllipseInRect(contex, rect);
//裁剪
CGContextClip(contex);
//将图片画上去
[self drawInRect:rect];
//获取图片
UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
//关闭
UIGraphicsEndImageContext();
return image;
}
```
** 因为绘图操作是 CPU密集型操作（会大量使用到 CPU），所以如果可以使用普通 UIView 来代替的就不要自己进行绘图**
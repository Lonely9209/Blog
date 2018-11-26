---
title: block
date: 2016-07-11 10:32:18
tags:
categories: OC
---

## 一、block的概念及基本使用
#### 1、block的基本概念
##### 1、什么是block？
   * block是iOS中一种比较特殊的数据类型。
   * block是苹果官方特别推荐使用的数据类型，应用场景比较广泛。

##### 2、使用场合？
    * 1）动画
    * 2）多线程
    * 3）集合遍历
    * 4）网络请求回调
##### 3、block的作用
    * 用来保存一段代码，可以在恰当的时间再取出来调用.

#### 2、block的基本用法
##### 1）简单的认识下block
  * 函数写法：
  
	  ```objc
	   void myBlock (){
	    NSLog(@“******”);
	  }
	  ```
  * block写法：
  
	```objc
	void (^myBlock)() =  ^{
	  NSLog(@“******”);
	}；
	myBlock();
	```
  * block格式：
  
	  ```objc
	  void (^block名称)(参数列表) = ^(参数列表){
	  // 代码实现；
	  }
	```
  * block结构的快速提示：
  
 		输入：inlineBlock

##### 2）定义block遍历，存储一段代码，这段代码的功能是能打印任意行数

```objc
void (^ myBlock)(int) = ^(int numberOfLines){
  for(int i = 0; i < numberOfLines ; i ++){
     NSLog(@“******”);
    }
  }
```
##### 3）定义block，计算两个整数的和.
```objc
int (^ sumBlock)(int,int) = ^(int num1, int num2){
     return num1 + num2;
};
int c = sumBlock(10,20);
NSLog(@“%d”,c);
```
##### 4）定义1个block，计算1个整数的平方
```objc
int (^squareBlock)(int);
squareBlock = ^(int sum){
   return sum * sum;
};
squareBlock(25);
```

## 二、block的typedef

```objc
int ( ^minusBlock)(int,int) = ^(int num1,int num2){
  return num1 - num2;
};
```
* 定义1个叫做:MyBlock的数据类型，它存储的代码必须返回int，，必须接受2个int类型参数

```objc
typedef int(^MyBlock)(int,int);
```
* 重命名之后，可这样使用：

	```objc
	MyBlock minusBlock = ^(int num1,int num2){
	  return num1 - num2;
	};
	```
	
## 三、block访问外部变量
#### 1、block访问外面变量。

```objc
int a = 10;
void (^block()) = ^{
  NSLog(@“%d”,a);
};
block();
```
#### 2、block修改外部变量值

```objc
__block int b = 10;
void (^block()) = ^{
 b = 20;
  NSLog(@“%d”,b);
};
block();
```
#### 3、block的快捷操作，名称写完整，直接敲回车.

## 四、block作为函数的返回值
#### 1、使用typedef定义一个新的类型
```objc
//给block起一个别名
typedef int(^newType)(int num1,int num2);
```
#### 2、使用新类型作为函数的返回值
```objc
//定义一个返回值是block类型的函数
newType test(){
//定义一个newType 类型的block变量
newType work1=^(int x,int y)
{
return x+y;
};
return work1;
}
```
#### 3、定义变量接收函数返回的值(block类型)
#### 4、调用block
```objc
//在main函数中调用返回值是newType类型的函数

// 定义block类型的变量n
newType n = test();

// 调用block，输出结果
NSLog(@"n = %d",n(10,20));
```

## 五、应用：block的使用场景（一）
#### 模拟上班场景
 * 用函数模拟周一~周五上班情景。

```objc
void goToWorkInDay1(){

    NSLog(@"起床");
    NSLog(@"刷牙");
    NSLog(@"穿衣服");

    NSLog(@"出门");
    NSLog(@"搭公交");

    NSLog(@"抵达公司");

    // 真正干的实事
    NSLog(@"熟悉项目业务");

    NSLog(@"下班");
    NSLog(@"搭公交");
    NSLog(@"回家");
    NSLog(@"睡觉");

}
void goToWorkInDay2(){

    NSLog(@"起床");
    NSLog(@"刷牙");
    NSLog(@"穿衣服");

    NSLog(@"出门");
    NSLog(@"搭公交");

    NSLog(@"抵达公司");

    // 真正干的实事
    NSLog(@"熟悉代码");

    NSLog(@"下班");
    NSLog(@"搭公交");
    NSLog(@"回家");
    NSLog(@"睡觉");

}
void goToWorkInDay3(){

    NSLog(@"起床");
    NSLog(@"刷牙");
    NSLog(@"穿衣服");

    NSLog(@"出门");
    NSLog(@"搭公交");

    NSLog(@"抵达公司");

    // 真正干的实事
    NSLog(@"编写代码");

    NSLog(@"下班");
    NSLog(@"搭公交");
    NSLog(@"回家");
    NSLog(@"睡觉");

}
```

* 函数抽取

```objc
	// 工作前做的事
	void goToWorkPrefix(){
	    NSLog(@"起床");
	    NSLog(@"刷牙");
	    NSLog(@"穿衣服");
	
	    NSLog(@"出门");
	    NSLog(@"搭公交");
	
	    NSLog(@"抵达公司");
	
	}
	// 工作后做的事
	void goToWorkPSubfix(){
	    NSLog(@"下班");
	    NSLog(@"搭公交");
	    NSLog(@"回家");
	    NSLog(@"睡觉");
	
	}
	// 第一天工作做的事
	void goToWorkInDay1(){
	   goToWorkPrefix()
	    NSLog(@"了解项目需求");
	    goToWorkPSsubfix();
	}
	// 第二天工作做的事
	void goToWorkInDay1(){
	   goToWorkPrefix()
	    NSLog(@"熟悉代码");
	    goToWorkPSsubfix();
	}
```
* 1.2）用函数抽取相同代码，提高复用性。

```objc
  void goToWork(void (^workBlock)()){
    static int a = 1;
    NSLog(@"goToWorkInDay%d",a);
    a++;

    NSLog(@"起床");
    NSLog(@"刷牙");
    NSLog(@"穿衣服");

    NSLog(@"出门");
    NSLog(@"搭公交");

    NSLog(@"抵达公司");

    // 真正干的实事
if (workBlock != nil){
    workBlock();
}

    NSLog(@"下班");
    NSLog(@"搭公交");
    NSLog(@"回家");
    NSLog(@"睡觉");

}

void goToWorkInDay1(){
  goToWork(^{
      NSLog(@"了解项目需求");
  });
}
void goToWorkInDay2(){
    goToWork(^{
        NSLog(@"熟悉代码");
    });
}

void goToWorkInDay3(){
    goToWork(^{
        NSLog(@"编写代码");
    });
}
```
* 1.3）使用block的注意事项：
 * 调用block时，不能传入空
 * 如：
 
```objc
int  main(){
 goToWork(nil);
};
```
* 1.4、使用循环语句抽取

```objc
void preWork(void (^workBlock)()){

    static int a = 1;
    NSLog(@"goToWorkInDay%d",a);
    a++;

    NSLog(@"起床");
    NSLog(@"刷牙");
    NSLog(@"穿衣服");
    NSLog(@"出门");
    NSLog(@"搭公交");
    NSLog(@"抵达公司");

    workBlock();

    NSLog(@"下班");
    NSLog(@"搭公交");
    NSLog(@"回家");
    NSLog(@"睡觉");

}
void goToWork(int day){
    switch (day) {
        case 1:
            preWork(^{
                NSLog(@"熟悉项目业务");
            });
            break;
        case 2:
            preWork(^{
                NSLog(@"熟悉代码");
            });

            break;
        case 3:
            preWork(^{
                NSLog(@"编写代码");
            });

            break;

        default:
            break;
    }
}


int main(int argc, const char * argv[]) {

    for(int i = 1; i < 4; i ++ ){
        goToWork(i);
    }
    return 0;
}

```

## 六、应用：block的使用场景（二）
* **思考：用返回值是block的函数，修改我们应用场景的代码**

``` objc
   // 1、重命名block
    typedef void (^workBlock)();


   // 2、block作为返回值使用
    workBlock workDay(int d){

   // 拿到循环体中的block
    workBlock work;

    switch (d) {
        case 1:
            // 真正干的实事
            work = ^{
                NSLog(@"熟悉项目代码");
            };
            break;
        case 2:
            // 真正干的实事
            work = (^{
                NSLog(@"熟悉项目代码");
            });
            break;
        case 3:
            // 真正干的实事
            work = (^{
                NSLog(@"编写代码");
            });
            break;
        case 4:
            // 真正干的实事
            work = (^{
                NSLog(@"扯淡");
            });
            break;
        case 5:
            // 真正干的实事
            work = (^{
                NSLog(@"聊天");
            });
            break;

        default:
            break;
    }
    // 返回循环体中的block内容
    return work;
}


// 3、修改工作日函数
void dayWork(int d){
    NSLog(@"------");
    NSLog(@"起床");
    NSLog(@"刷牙");
    NSLog(@"穿衣服");
    NSLog(@"出门");
    NSLog(@"搭公交");
    NSLog(@"抵达公司");

   // 拿到重命名的block的返回值
    workBlock work = workDay(d);
    // 调用block
    work();

    NSLog(@"下班");
    NSLog(@"搭公交");
    NSLog(@"回家");
    NSLog(@"睡觉");

}

```








---
title: UITextField
date: 2016-07-09 16:20:33
tags:
categories: UI
---

# 一、UITextField属性
设置和获取文字框文字
```objc		
@property(nonatomic,copy) NSString *text;
```
通过AttributedString创建和获取文字
```objc	
@property(nonatomic,copy) NSAttributedString *attributedText；
```
设置字体颜色属性
```objc	
@property(nonatomic,retain) UIColor *textColor;
```
设置字体属性
```objc	
@property(nonatomic,retain) UIFont *font;
```
设置字体对齐格式
```objc	
@property(nonatomic)NSTextAlignment textAlignment;
```
设置输入框风格
```objc	
@property(nonatomic) UITextBorderStyle borderStyle;

UITextBorderStyleNone,			//没有任何边框
UITextBorderStyleLine,			//线性边框
UITextBorderStyleBezel,			//阴影效果边框
UITextBorderStyleRoundedRect	//原型效果边框
```
设置默认字体属性
```objc	
@property(nonatomic,copy) NSDictionary *defaultTextAttributes；
```
**注：这个属性的设置会影响到全部字体的属性。**
设置占位图片
```objc	
@property(nonatomic,copy) NSString *placeholder;
@property(nonatomic,copy) NSAttributedString *attributedPlaceholder；
```
设置是否在开始编辑时清空输入框内容
```objc	
@property(nonatomic) BOOL clearsOnBeginEditing;
```
设置字体大小是否随宽度自适应（默认为NO）
```objc	
@property(nonatomic) BOOL adjustsFontSizeToFitWidth;
```
设置最小字体大小
```objc	
@property(nonatomic) CGFloat minimumFontSize;
```
设置背景图片（会被拉伸）
```objc	
@property(nonatomic,retain) UIImage *background;
@property(nonatomic,retain) UIImage *disabledBackground;
```
设置清除按钮的显示模式
```objc	
@property(nonatomic) UITextFieldViewMode clearButtonMode;

UITextFieldViewModeNever,			//从不显示
UITextFieldViewModeWhileEditing,	//编辑的时候显示
UITextFieldViewModeUnlessEditing,	//非编辑的时候显示
UITextFieldViewModeAlways			//任何时候都显示
```
设置输入框左/右边的view
```objc	
@property(nonatomic,retain) UIView *leftView;
@property(nonatomic,retain) UIView *rightView;
```
设置输入框左/右视图的显示模式
```objc	
@property(nonatomic) UITextFieldViewMode leftViewMode;
@property(nonatomic) UITextFieldViewMode rightViewMode;
```
设置输入框成为第一响应时弹出的视图和辅助视图（类似键盘）
```objc	
@property (readwrite, retain) UIView *inputView;
@property (readwrite, retain) UIView *inputAccessoryView;
```
这个属性设置是否允许再次编辑时在内容中间插入内容
```objc	
@property(nonatomic) BOOL clearsOnInsertion；
```
密码文本框是暗文(UITextInputTraits) 
```objc	
@property(nonatomic,getter=isSecureTextEntry) BOOL secureTextEntry;       
```

### 补充三个不常用方法（UIKeyInput）
判断UITextField当前是否有内容
```objc			
- (BOOL)hasText;
```
从键盘输入内容就会调用此方法（中文无效）
```objc	
- (void)insertText:(NSString *)text;
```
当击UITextField右边大删除按钮
```objc	
- (void)deleteBackward;
```
	
# 二、UITextField 设置监听的3种方式:

### 1、通过addTarget方式

* UITextField继承UIControll,可以addTarget监听。
* 但是这种方式只能监听一些"单击事件"、"滚动条滚动事件"等, 有些事件通过addTarget方式监听无效, 比如"Value Changed"事件。

### 2、通过代理
* 文本框的代理协议"UITextFieldDelegate"。
* 演示:textFieldShouldBeginEditing方法。

```objc
// 为某个文本框设置代理
- (void)viewDidLoad {
    [super viewDidLoad];
    // 设置文本框代理为当前控制器。
    self.textFidld.delegate = self;
 }

// 让当前控制器遵守UITextFieldDelegate协议, 并且实现- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField方法, 返回YES
- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField
{
    return YES;
}
```


### 3、通过"通知中心"监听事件。

** 思路: 让用户名、密码文本框同时设置监听到textChanged方法。**

```objc
//参考代码:

- (void)viewDidLoad {
    [super viewDidLoad];
//通过通知中心监听文本框内容变化, 添加观察者
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(textFieldValueChanged) name:UITextFieldTextDidChangeNotification object:self.userNameTextField];

    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(textFieldValueChanged) name:UITextFieldTextDidChangeNotification object:self.passWordTextField];

//TextField通知名
//    UIKIT_EXTERN NSString *const UITextFieldTextDidBeginEditingNotification;
//    UIKIT_EXTERN NSString *const UITextFieldTextDidEndEditingNotification;
//    UIKIT_EXTERN NSString *const UITextFieldTextDidChangeNotification;
}

//实现监听方法
- (void) textFieldValueChanged
{
    //文本框的值发送改变, 就会调用
}

/**** 注意通过通知的方式注册事件, 需要在dealloc方法中移除 ****/
// 移除通知监听, 观察者被销毁时, 在观察着的dealloc方法中移除
- (void)dealloc
{
    //移除观察者
    [[NSNotificationCenter defaultCenter]removeObserver:self];
}
```
### 4、Segue对象。
* 在xcode6.1下, 拖线时选择Segue的style为show, 等价于以前的push。

* 重新拖线（手动 Segue）, 从控制器 to 控制器。

```objc
//参考代码 :
[self performSegueWithIdentifier:@"login2contact" sender:@"Jack"];

//实现控制器的- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender方法。


- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
// Get the new view controller using [segue destinationViewController].
// Pass the selected object to the new view controller.
//    NSLog(@"%@",sender);
//获取目标控制器
    UIViewController * targetVC = segue.destinationViewController;

}
```

```objc
//performSegueWithIdentifier:sender:的执行过程
[self performSegueWithIdentifier:@"login2contacts" sender:nil];
//1> self是来源控制器，只能通过来源控制器来调该方法。
//2> 根据identifier去storyboard中找到对应的线，新建UIStoryboardSegue对象
//3> 设置Segue对象的sourceViewController（来源控制器）
//4> 新建并且设置Segue对象的destinationViewController（目标控制器）
//5> 调用- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender方法
//6> 执行跳转。
//** 具体跳转步骤:
//1> 通过segue对象的sourceViewController获取源控制器所在的导航控制器。
//2> 将segue的destinationViewControlelr压栈(push)进去。
```

### 5、提示框的弹出

```objc
//xcode7下
- (IBAction)logOutBtnClick:(id)sender
{
    UIAlertController * alert = [UIAlertController alertControllerWithTitle:@"确定注销吗?" message:nil preferredStyle:UIAlertControllerStyleActionSheet];

    UIAlertAction * okAction = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDestructive handler:^(UIAlertAction * _Nonnull action) {
    [self.navigationController popViewControllerAnimated:YES];
    }];

    UIAlertAction * cancel = [UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:nil];

    [alert addAction:okAction];

    [alert addAction:cancel];

    [self presentViewController:alert animated:YES completion:nil];
}
```

```objc
// xcode6

// 1. 弹出UIActionSheet对话框
UIActionSheet *sheet = [[UIActionSheet alloc] initWithTitle:@"确定要注销" delegate:self cancelButtonTitle:@"取消" destructiveButtonTitle:@"注销" otherButtonTitles:nil, nil];

//alertView 上添加一个 textField
/*
UIAlertViewStyleDefault = 0,
UIAlertViewStyleSecureTextInput,//安全的文本输入, 密码保护模式
UIAlertViewStylePlainTextInput,//普通的文本输入
UIAlertViewStyleLoginAndPasswordInput//两行输入,Login 和 Password
*/
alertView.alertViewStyle = UIAlertViewStylePlainTextInput;

//获取 alertView上的 textField
UITextField *textField = [alertView textFieldAtIndex:0];

[sheet showInView:self.view];

```


* UIActionSheet中的按钮点击事件

```objc
- (void)actionSheet:(UIActionSheet *)actionSheet clickedButtonAtIndex:(NSInteger)buttonIndex
{
    //buttonIndex 对应按钮的下标
}
```
### awakeFromNib
* awakeFromNib什么时候调用？xib加载完成的时候调用
* awakeFromNib的作用:从控件从xib加载完成之后，做一些初始化操作。
* 在layoutSubViews设置尺寸。

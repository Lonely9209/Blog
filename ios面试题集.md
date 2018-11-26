---
title: ios面试题集
date: 2016-07-19 15:22:55
tags:
categories: 随笔
---
# 一、OC基本概念
### 1.static 关键字的作用
（1）函数体内 **static** 变量的作用范围为该函数体，不同于 auto 变量，该变量的内存只被分配一次， 因此其值在下次调用时仍维持上次的值；   
（2）在模块内的 static 全局变量可以被模块内所有函数访问，但不能被模块外其它函数访问；  
（3）在类中的 static 成员变量属于整个类所拥有，对类的所有对象只有一份拷贝； 
### 2.关键字const有什么含义
const意味着“只读”  
本质：const在谁后面谁就不可修改，const在最前面则将其后移一位即可,二者等效

```objc
const int a;
int const a;
const int *a;
int * const a;
int const * a const;
```

前两个的作用是一样，a是一个常整型数。第三个意味着a是一个指向常整型数的指针（也就是，指向的整型数是不可修改的，但指针可以，此最常见于函数的参数，当你只引用传进来指针所指向的值时应该加上const修饰符，程序中修改编译就不通过，可以减少程序的bug）。  
第四个意思a是一个指向整型数的常指针（也就是说，指针指向的整型数是可以修改的，但指针是不可修改的）。最后一个意味着a是一个指向常整型数的常指针（也就是说，指针指向的整型数是不可修改的，同时指针也是不可修改的）。  
const关键字至少有下列n个作用：  
（1）欲阻止一个变量被改变，可以使用const关键字。在定义该const变量时，通常需要对它进行初始化，因为以后就没有机会再去改变它了；  
（2）对指针来说，可以指定指针本身为const，也可以指定指针所指的数据为const，或二者同时指定为const；  
（3）在一个函数声明中，const可以修饰形参，表明它是一个输入参数，在函数内部不能改变其值；  
（4）对于类的成员函数，若指定其为const类型，则表明其是一个常函数，不能修改类的成员变量；  
（5）对于类的成员函数，有时候必须指定其返回值为const类型，以使得其返回值不为“左值”。例如：

```objc
const classA operator*(const classA& a1,const classA& a2); 
　　operator*的返回结果必须是一个const对象。如果不是，这样的变态代码也不会编译出错：
classA a, b, c;
(a * b) = c; // 对a*b的结果赋值 
操作(a * b) = c显然不符合编程者的初衷，也没有任何意义。
```

### 3.OC中有哪些数据存储方式,各有什么区别
**四种存储方式: **  
1.NSUserDefaults,用于存储配置信息  
2.SQLite,用于存储查询需求较多的数据  
3.CoreData,用于规划应用中的对象(MagicalRecord)  
4.使用基本对象类型定制的个性化缓存方案.(归档)   
NSUserDefaults:对象中储存了系统中用户的配置信息,开发者可以通过这个实例对象对这些已有的信息进行修改,也 可以按照自己的需求创建新的配置项。   
SQLite擅长处理的数据类型其实与NSUserDefaults差不多,也是基础类型的小数据,只是从组织形式上不同。开发者可以以关系型数据库的方式组织数据,使用SQL DML来管理数据。一般来说应用中的格式化的文本类数据可以存放在数据库 中,尤其是类似聊天记录、Timeline等这些具有条件查询和排序需求的数据。   
CoreData是一个管理方案,它的持久化可以通过SQLite、XML或二进制文件储存。它可以把整个应用中的对象建模并进 行自动化的管理。从归档文件还原模型时CoreData并不是一次性把整个模型中的所有数据都载入内存,而是根据运行时状 态,把被调用到的对象实例载入内存。框架会自动控制这个过程,从而达到控制内存消耗,避免浪费。 无论从设计原理还是使用方法上看,CoreData都比较复杂。因此,如果仅仅是考虑缓存数据这个需求,CoreData绝对不 是一个优选方案。CoreData的使用场景在于:整个应用使用CoreData规划,把应用内的数据通过CoreData建模,完全 基于CoreData架构应用。   
使用基本对象类型定制的个性化缓存方案:从需求出发分析缓存数据有哪些要求:按Key查找,快速读取,写入不影响正常 操作,不浪费内存,支持归档。这些都是基本需求,那么再进一步或许还需要固定缓存项数量,支持队列缓存,缓存过期等。

### 4.ios平台怎么做数据的持久化?coredata和sqlite有无必然联系?coredata是一个关系型数据库吗?
**iOS中可以有四种持久化数据的方式: 属性列表、对象归档、SQLite3和Core Data**    
coredata可以使你以图形界面的方式快速的定义app的数据模型,同时在你的代码中容易获取到它。coredata提供了基础结构去处 理常用的功能,例如保存,恢复,撤销和重做,允许你在app中继续创建新的任务。在使用coredata的时候,你不用安装额外的数据 库系统,因为coredata使用内置的sqlite数据库。coredata将你app的模型层放入到一组定义在内存中的数据对象。coredata会 追踪这些对象的改变,同时可以根据需要做相应的改变,例如用户执行撤销命令。当coredata在对你app数据的改变进行保存的时 候,core data会把这些数据归档,并永久性保存。    
mac os x中sqlite库,它是一个轻量级功能强大的关系数据引擎,也很容易嵌入到应用程序。可以在多个平台使用,sqlite是一个轻 量级的嵌入式sql数据库编程。与coredata框架不同的是,sqlite是使用程序式的,sql的主要的API来直接操作数据表。  
Core Data不是一个关系型数据库,也不是关系型数据库管理系统(RDBMS)。虽然Core Dta支持SQLite作为一种存储类型, 但它不能使用任意的SQLite数据库。Core Data在使用的过程种自己创建这个数据库。Core Data支持对一、对多的关系。
### 5.堆栈的区别（Heap是堆，stack是栈）
(1)管理方式:对于栈来讲,是由编译器自动管理,无需我们手工控制;对于堆来说,释放工作由程序员控制,容易产生 memory leak。   
(2)申请大小:能从栈获得的空间较小,堆是向高地址扩展的数据结构,是不连续的内存区域。堆的大小受限于计算机系统中 有效的虚拟内存。由此可见,堆获得的空间比较灵活,也比较大。   
(3)碎片问题:对于堆来讲,频繁的new/delete势必会造成内存空间的不连续,从而造成大量的碎片,使程序效率降低。 对于栈来讲,则不会存在这个问题,因为栈是先进后出的队列,他们是如此的一一对应,以至于永远都不可能有一个内存块 从栈中间弹出   
(4)分配方式:堆都是动态分配的,没有静态分配的堆。栈有2种分配方式:静态分配和动态分配。静态分配是编译器完成 的,比如局部变量的分配。动态分配由 alloca函数进行分配,但是栈的动态分配和堆是不同的,他的动态分配是由编译器 进行释放,无需我们手工实现。   
(5)分配效率:栈是机器系统提供的数据结构,计算机会在底层对栈提供支持:分配专门的寄存器存放栈的地址,压栈出栈 都有专门的指令执行,这就决定了栈的效率比较高。堆则是C/C++函数库提供的,它的机制是很复杂的。  

```objc
Stack的空间由操作系统自动分配/释放，Heap上的空间手动分配/释放。Stack空间有限，Heap是很大的自由存储区  
C中的malloc函数分配的内存空间即在堆上,C++中对应的是new操作符。  
程序在编译期对变量和函数分配内存都在栈上进行,且程序运行过程中函数调用时参数的传递也在栈上进行
```
### 6.面向对象的三大特征,并作简单的介绍
**面向对象的三个基本特征是:封装、继承、多态。**  
封装是面向对象的特征之一,是对象和类概念的主要特性。 封装,也就是把客观事物封装成抽象的类,并且类可以把自己的数据和方法只让可信的类或者对象操作,对不可信的进行信息隐藏。隐藏对象的属性和实现细节,仅对外公开接口,提高代 码安全性,封装程度越高,独立性越强,使用越方便。   
继承是指这样一种能力:它可以使用现有类的所有功能,并在无需重新编写原来的类的情况下对这些功能进行扩展。 通过 继承创建的新类称为“子类”或“派生类”。 被继承的类称为“基类”、“父类”或“超类”   
多态性:允许你将父对象设置成为和一个或更多的他的子对象相等的技术,赋值之后,父对象就可以根据当前赋值给它的子 对象的特性以不同的方式运作。简单的说,就是一句话:**允许将子类类型的指针赋值给父类类型的指针**
### 7.我们说的obc是动态运行时语言是什么意思
**多态**。主要是将数据类型的确定由编译时,推迟到了运行时。这个问题其实涉及到两个概念,运行时和多态。简单来说, 运行时机制使我们直到运行时才去决定一个对象的类别,以及调用该类别对象指定方法。多态:不同对象以自己的方式响应 相同的消息的能力叫做多态。意思就是假设生物类(life)都用有一个相同的方法-eat;那人类属于生物,猪也属于生物,都 继承了life后,实现各自的eat,但是调用是我们只需调用各自的eat方法。也就是不同的对象以自己的方式响应了相同的消 息(响应了eat这个选择器)。因此也可以说,运行时机制是多态的基础
### 8.readwrite,readonly,assign,retain,copy,nonatomic 、strong、weak属性的作用?并区别strong(强引用)、 weak(弱引用)?什么情况使用copy,assign,和retain?
readwrite 是可读可写特性;需要生成getter方法和setter方法时  
readonly 是只读特性 只会生成getter方法 不会生成setter方法 ;不希望属性在类外改变  
assign 是赋值特性,setter方法将传入参数赋值给实例变量;仅设置变量时;  
retain 表示持有特性,setter方法将传入参数先保留,再赋值,传入参数的retaincount会+1;  
copy 表示赋值特性,setter方法将传入对象复制一份;需要完全一份新的变量时。  
nonatomic 非原子操作,决定编译器生成的setter getter是否是原子操作,atomic表示多线程安全,一般使用 nonatomic  
assign用于简单数据类型,如NSInteger,double,bool。  
retain 和copy用户对象,copy用于当 a指向一个对象,b也想指向同样的对象的时候,如果用assign,a如果释放,再 调用b会crash,如果用copy 的方式,a和b各自有自己的内存,就可以解决这个问题。retain 会使计数器加1,也可以解 决assign的问题。另外:atomic和nonatomic用来决定编译器生成的getter和setter是否为原子操作。 在多线程环境 下,原子操作是必要的,否则有可能引起错误的结果。
### 9.分别描述内存管理要点、autorelease、release、NSAutoreleasePool?并说明autorelease是什 么时候被release的?简述什么时候由你负责释放对象,什么时候不由你释放?[NSAutoreleasePool release]和[NSAutoreleasePool drain]有什么区别?
**内存管理要点:**  
内存管理:管理堆区内存的分配和释放；  
Objective-C 使用引用计数机制(retainCount)来管理内存。内存每被引用一次,该内存的引用计数+1,每被释放一次引 用计数-1。当引用计数 = 0 的时候,调用该对象的 dealloc 方法,来彻底从内存中删除该对象。  
alloc,allocWithZone,new(带初始化)时:该对象引用计数 +1;  
retain:手动为该对象引用计数 +1;  
copy:对象引用计数 +1;  
mutableCopy:生成一个新对象,新对象引用计数为 1;  
release:手动为该对象引用计数 -1; autorelease:把该对象放入自动释放池,当自动释放池释放时,其内的对象引用计数 -1。  
**NSAutoreleasePool:**   
NSAutoreleasePool是通过接收对象向它发送的autorelease消息,记录该对象的release消息,当自动释放池被销毁 时,会自动向池中的对象发送release消息。  
autorelease 是在自动释放池被销毁,向池中的对象发送release只能释放自己拥有的对象,   
**区别是**:在引用计数环境下(在不使用ARC情况下),两者基本一样,在GC环境下,release 是一个no-op(无效操 作),所以无论是不是gc都使用drain
### 10.浅复制和深复制的区别
浅层复制(copy):只复制指向对象的指针,而不复制引用对象本身。//通过对象的指针来访问这个对象----只复制地址   
深层复制(mutableCopy):复制引用对象本身---再创建一个对象 意思就是有个A对象,复制一份后得到A_copy对象后,对于浅复制来说,A和A_copy指向的是同一个内存资源,复制的 只不过是是一个指针,对象本身资源 还是只有一份,那如果我们对A_copy执行了修改操作,那么发现A引用的对象同样被修改,这其实违背了我们复制拷贝的一 个思想。深复制就好理解了,内存中存在了两份独立对象本身。//当修改A时,A copy不变
### 11.什么是安全释放
MRC中，要释放一个对象  
[obj release]; 释放对象之后，不会修改对象的指针地址，后续如果给 obj 发送消息，就会出现也指针错误  
[obj nil]; 对象地址已经指向NULL，这个时候再发任何消息也不会报错。  
僵尸对象：已经被释放的对象  
野指针：指向僵尸对象的指针  
空指针：nil，给空指针发送任何消息都不会报错

### 12.NULL与nil/NSNULL
nil：是地址指向NULL的空对象，在OC/C++中，给nil对象发送任何消息，都不会报错  
NULL：是空地址，本身就是0,就是一个整数，不能给NULL发送消息  
NSNULL：[NSNULL null]; 用处：就是向NSDictionary/NSArray中添加空对象使用的，只要用在通过运行时机制或KVC动态给对象属性设置数值时使用### 13.obj-c的优缺点
**objc优点：**   
  1) Cateogies   
  2) Posing   
  3) 动态识别   
  4) Objective-C 与 C++ 可混合编程 
**缺点:**   
  1) 不支持命名空间   
  2)  不支持运算符重载   
  3）不支持多重继承   
  4）使用动态运行时类型，所有的方法都是函数调用，所以很多编译时优化方法都用不到。（如内联函数等），性能低劣。
### 14.什么时候用delegate，什么时候用Notification？
答：delegate针对one-to-one关系，并且reciever可以返回值 给sender，notification 可以针对one-to-one/many/none,reciever无法返回值给sender.所以，delegate用于sender希望接受到 reciever的某个功能反馈值，notification用于通知多个object某个事件。 
### 15.什么是KVC和KVO
KVC:键 - 值编码是一种间接访问对象的属性使用字符串来标识属性，而不是通过调用存取方法，直接或通过实例变量访问的机制。  
KVC(Key-Value-Coding)内部的实现：一个对象在调用setValue的时候，（1）首先根据方法名找到运行方法的时候所需要的环境参数。（2）他会从自己isa指针结合环境参数，找到具体的方法实现的接口。（3）再直接查找得来的具体的方法实现。
KVO:键值观察机制，他提供了观察某一属性变化的方法，极大的简化了代码    
KVO（Key-Value- Observing）：当观察者为一个对象的属性进行了注册，被观察对象的isa指针被修改的时候，isa指针就会指向一个中间类，而不是真实的类。所以 isa指针其实不需要指向实例对象真实的类。所以我们的程序最好不要依赖于isa指针。在调用类的方法的时候，最好要明确对象实例的类名
的类名
```objc
[self addObserver:self forKeyPath:@"highlighted" options:0 context:nil];   
#pragma mark KVO   
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context {
	if([keyPath isEqualToString:@"highlighted"]) {
	[self setNeedsDisplay];
	}
}
```
**KVC:破坏类的封装性可以直接访问属性**

### 16.KVO的底层实现原理（runTime运行时）
1.（添加监听者时）保存所有参数（监听者、枚举、携带参数、路径）
2.创建子类NSKVONotifying-（类名），并重写set方法
3.set方法，监听者底层调用自己的observeValueForKeyPath
##### 注：
Notification 是观察者模式的实现,KVO是观察者模式的OB-C底层实现。  
NOtification 通过 Notifydcation addobserver 和 remove observer 工作。   
KVO是键值监听,键值观察机制,提供了观察某一属性变化的方法   
KVC是键值编码,是一种间接访问对象的属性,使用字符串来标示属性(例如:setValue:forKey:)   
Delegate:把某个对象要做的事情委托给别的对象去做。那么别的对象就是这个对象的代理,代替它来打理要做的事。反映到程序中,首先要明确一个对象的委托方是哪个对象,委托所做的内容是什么。

### 17.类别的作用？继承和类别在实现中有何区别？
答案：category 可以在不获悉，不改变原来代码的情况下往里面添加新的方法，只能添加，不能删除修改。并且如果类别和原来类中的方法产生名称冲突，则类别将覆盖原来的方法，因为类别具有更高的优先级。  
类别主要有3个作用：  
(1)将类的实现分散到多个不同文件或多个不同框架中。  
(2)创建对私有方法的前向引用。  
(3)向对象添加非正式协议。  
继承可以增加，修改或者删除方法，并且可以增加属性。
### 18.类别和类扩展的区别
category和extensions的不同在于 后者可以添加属性。另外后者添加的方法是必须要实现的。分类的小括号中必须有名字，分类只能扩充方法，不能扩充属性和成员变量。(Category只能为对象添加方法,却不能添加成员变量的原因:如果可以添加成员变量,添加的成员变量没有办法初始 化----这是语言规则)  
extensions可以认为是一个私有的Category
**分类中能不能添加属性呢？**
如果通过@Property给分类新增属性，那么@property只会生成该属性的getter和setter方法的声明，而不会生成属性和实现方法。
### 19.为什么不能包含分类的.m文件
因为这样会重复包含另一个类的实现文件
### 20.方法和选择器有何不同
selector是一个方法的名字，method是一个组合体，包含了名字和实现
### 21.OC有多继承吗?没有的话可以用什么方法替---多继承即一个子类可以有多个父类,它继承了多个父类的特性。
Object-c的类没有多继承,只支持单继承,如果要实现多继承的话,可以通过类别和协议的方式来实现,OC类似于多继承,是在用protocol委托代理来实现的;可以实现多个接口,通过实现多个接口可以完成C++的多重继承;Category是类别,一 般情况用分类好,用Category去重写类的方法,仅对本Category有效,不会影响到其他类与原有类的关系。
### 22.Objective-C有私有方法么?私有变量呢?如果没有的话,有没有什么代替的方法?
objective-c类里面的方法只有两种, 静态方法和实例方法.但是可以通过把方法的声明和定义都放在.m文件中来实现一个表面上的私有方法。有私有变量,可以通过@private来修饰,或者把声明放到.m文件中。在Objective‐C中,所有实例变 量默认都是私有的,所有实例方法默认都是公有的
### 23.#import、#include和@class有什么区别
@class一般用于头文件中需要声明该类的某个实例变量的时候用到,它只是声明了一个类名,关于这个类的内部实现都没 有告诉编译器,在m文件中还是需要使用#import。而#import比起#include的好处就是不会引起交叉编译。
### 24.谈谈你对MVC的理解?为什么要用MVC?在Cocoa中MVC是怎么实现的?你还熟悉其他的OC设计模式或别 的设计模式吗?
MVC就是Model-View-Controller的缩写,M指的是业务模型,V指的是用户页面,C指的是控制器。MVC是架构模式,是讲M和 V的代码分离,从而使同那个一个程序可以使用不同的表现形式。   
M:表示数据和业务规则,V是用户看到的并与之交互的页面,C是接受用户的输入并调用M和V取完成用户需求的 单例,代理,观察者,工厂模式等   
单例模式:说白了就是一个类不通过alloc方式创建对象,而是用一个静态方法返回这个类的对象。系统只需要拥有一个的 全局对象,这样有利于我们协调系统整体的行为;   
代理模式:代理模式给某一个对象提供一个代理对象,并由代理对象控制对源对象的引用.比如一个工厂生产了产品,并不想 直接卖给用户,而是搞了很多代理商,用户可以直接找代理商买东西,代理商从工厂进货.常见的如QQ的自动回复就属于代 理拦截,代理模式在iphone中得到广泛应用.  
观察者模式: 当一个物体发生变化时,会通知所有观察这个物体的观察者让其做出反应。实现起来无非就是把所有观察者的 对象给这个物体,当这个物体的发生改变,就会调用遍历所有观察者的对象调用观察者的方法从而达到通知观察者的目的;
### 25.c和obj-c如何混用?
1)obj-c的编译器处理后缀为m的文件时,可以识别obj-c和c的代码,处理mm文件可以识别obj-c,c,c++代码,但cpp文件必须只能用c/c++代码,而且cpp文件include的头文件中,也不能出现obj- c的代码,因为cpp只是cpp。  
2) 在mm文件中混用cpp直接使用即可,所以obj-c混cpp不是问题  
3)在cpp中混用obj- c其实就是使用obj-c编写的模块是我们想要的。如果模块以类实现,那么要按照cpp class的标准写类的定 义,头文件中不能出现obj-c的东西,包括#import cocoa的。 实现文件中,即类的实现代码中可以使用obj-c的东西,可以import,只是后缀是mm。如果模块以函数实现,那么头文件要按 c的格 式声明函数,实现文件中,c++函数内部可以用obj-c,但后缀还是mm或m。总结:只要cpp文件和cpp include的文件中不包含 obj-c的东西就可以用了,cpp混用obj-c的关键是使用接口,而不能直接使用实现代码,实际上cpp混用的是obj-c编译后的o文 件,这个东西其实是无差别的,所以可以用。obj-c的编译器支持cpp
### 26.举出5个以上你所熟悉的ios sdk库有哪些和第三方库有哪些?
ios-sdk:Foundation.framework,CoreGraphics.framework,UIKit.framework, MediaPlayer.framework, CoreAudio.framework第三方库: 1.json编码解码MJExtension;2.ASIHTTPRequest等相关协议封装;3.MJRefresh下拉刷新代码;4.AsyncImageView 异步加载图片并缓存;5.SDWebImage——简化网络图片处理6.SVProgressHUD;7.AFNetworking
### 27.UITableView的重用机制?如何在一个view上显示多个tableView,tableView要求不同的数据源以及不同的样式 (要求自定义cell), 如何组织各个tableView的delegate和dataSource?请说说实现思路?在一个tableView 中需要自定义多种样式的cell(两种或三种),通常你如何实现,说说思路即可?UITableView的那些元素是可以自定义的?
**UITableView的重用机制:**
查看UITableView头文件,会找到NSMutableArray*visiableCells,和NSMutableArray*reusableTableCells两 个结构。visiableCells内保存当前显示的cells,reusableTableCells保存可重用的cells。 TableView显示之初,reusableTableCells为空,那么tableView dequeueReusableCellWithIdentifier:CellIdentifier返回nil。开始的cell都 是通过 [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier] 来创 建,而且cellForRowAtIndexPath只是调用最大显示cell数的次数。 比如:有100条数据,iPhone一屏最多显示10个cell。程序最开始显示TableView的情况是:1. 用[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier] 创建10次cell,并给cell指定同样的重用标识(当然,可以为不同显示类型的cell指定不同的标识)。并且10个cell全部都加 入到 visiableCells数组,reusableTableCells为空。2. 向下拖动tableView,当cell1完全移出屏幕,并且 cell11(它也是alloc出来的,原因同上)完全显示出来的时候。 cell11加入到visiableCells,cell1移出 visiableCells,cell1加入到reusableTableCells。3. 接着向下拖动tableView,因为reusableTableCells中已 经有值,所以,当需要显示新的cell, cellForRowAtIndexPath再次被调用的时 候,tableView dequeueReusableCellWithIdentifier:CellIdentifier,返回cell1。 cell1加入到visiableCells,cell1 移出reusableTableCells;cell2移出 visiableCells,cell2加入到reusableTableCells。之后再需要显示的Cell就可 以正常重用了􏰂

### 28.OC中的数字对象都有哪些,简述它们与基本数据类型的区别是什么
Objective-C中的数字对象NSNumber;   
Objective-C中的基本类型和C语言中的基本类型一样.主要有:int,long,float,double,char,void,bool等. 对于基本类型变量,不需要用指针,也不用手动回收,方法执行结束会自动回收.数字对象需要指针,也需要手动回收内存。
### 29.OC中所有对象间的交互是如何实现的?
**通过指针实现的**
### 30.objective-c中的类型转换分为哪几类?
可变与不可变之间的转化;   
可变与可变之间的转化;  
不可变与不可变之间。
### 31.类变量的@protected ,@private,@public,@package声明各有什么含义?
```objc
变量的作用域不同,@protected 该类和所有子类中 的方法可以直接访问这样的变量,这是默认的;  
@private 该类中的方法可以访问这样的变量,子类不可以; @public除了自己和子类方法外,也可以被其他类或者其他模块中的方法访问;  
@package 目前尚未得出结论
```
### 32.什么是序列化或者Acrchiving,可以用来做什么,怎样与copy结合,原理是什么?.
**序列化就是:归档**
### 33.NSArray和NSMutableArray的区别,多线程操作哪个更安全?
**NSArray更安全,当同时被访问时,NSArray是不可改变**
### 34.当前有一个数组,里面有若干重复的数据,如何去除重复的数据?
**可以由数组,到集合**
### 35.isKindOfClass、isMemberOfClass、selector作用分别是什么
isKindOfClass,作用是,某个对象属于某个类型,包括继承的类型---  
isMemberOfClass:某个对象确切属于某个类型,是不是具体的实例  
selector:通过方法名,获取在内存中的函数的入口地址
### 36.写出下面程序段的输出结果
```objc
NSDictionary *dict = [NSDictionary dictionaryWithObject:@"a string value" forKey:@"akey"];  
NSLog(@"%@", [dict objectForKey:@"akey"]);  
[dict release];
```
**打印输出 a string value,然后崩溃----原因:便利构造器创建的对象,之后的release,会造成过度释放**
### 37.请写出以下代码的执行结果
```objc
NSString * name = [ [ NSString alloc] init ];  
name = @”Habb”;  
[name release];
```
打印输出结果是: Habb,在[name release]前后打印均有输出结果 ---会造成内存泄露---原先指向的区域变成了野指针,之后的释放,不能释放之前创建的区域

# 二、网络与多线程
### 1.HTTP协议详解
HTTP是一个属于应用层的面向对象的协议，由于其简捷、快速的方式，适用于分布式超媒体信息系统。目前在WWW中使用的是HTTP/1.0的第六版，HTTP/1.1的规范化工作正在进行之中。  
http（超文本传输协议）是一个基于请求与响应模式的、无状态的、应用层的协议，常基于TCP的连接方式，HTTP1.1版本中给出一种持续连接的机制，绝大多数的Web开发，都是构建在HTTP协议之上的Web应用。  
HTTP协议的主要特点可概括如下：  
1.支持客户/服务器模式。  
2.简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。  
3.灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。  
4.无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。(短连接)  
5.无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。
### 2.URL
HTTP URL (URL是一种特殊类型的URI是他的子类，包含了用于查找某个资源的足够的信息)的格式如下：  
http://host[":"port][abs_path]  
http表示要通过HTTP协议来定位网络资源；host表示合法的Internet主机域名或者IP地址；port指定一个端口号，为空则使用缺省端口80;
abs_path指定请求资源的URI；如果URL中没有给出abs_path，那么当它作为请求URI时，必须以“/”的形式给出，通常这个工作浏览器自动帮我们完成。
### 3.TCP/UDP区别联系
TCP---传输控制协议,提供的是面向连接、可靠的字节流服务。当客户和服务器彼此交换数据前，必须先在双方之间建立一个TCP连接，之后才能传输数据。TCP提供超时重发，丢弃重复数据，检验数据，流量控制等功能，保证数据能从一端传到另一端。   
UDP---用户数据报协议，是一个简单的面向数据报的运输层协议。UDP不提供可靠性，它只是把应用程序传给IP层的数据报发送出去，但是并不能保证它们能到达目的地。由于UDP在传输数据报前不用在客户和服务器之间建立一个连接，且没有超时重发等机制，故而传输速度很快 
##### 注：
TCP（Transmission Control Protocol，传输控制协议）是基于连接的协议，也就是说，在正式收发数据前，必须和对方建立可靠的连接。一个TCP连接必须要经过三次“对话”才能建立起来，我们来看看这三次对话的简单过程：1.主机A向主机B发出连接请求数据包；2.主机B向主机A发送同意连接和要求同步（同步就是两台主机一个在发送，一个在接收，协调工作）的数据包；3.主机A再发出一个数据包确认主机B的要求同步：“我现在就发，你接着吧！”，这是第三次对话。三次“对话”的目的是使数据包的发送和接收同步，经过三次“对话”之后，主机A才向主机B正式发送数据。 
UDP（User Data Protocol，用户数据报协议）是与TCP相对应的协议。它是面向非连接的协议，它不与对方建立连接，而是直接就把数据包发送过去！  UDP适用于一次只传送少量数据、对可靠性要求不高的应用环境。 
tcp协议和udp协议的差别 
是否连接面向连接面向非连接 
传输可靠性可靠不可靠 
应用场合传输大量数据少量数据 
速度慢快
### 4.socket连接和http连接的区别
简单说，你浏览的网页（网址以http://开头)都是http协议传输到你的浏览器的, 而http是基于socket之上的。socket是一套完成tcp，udp协议的接口。  
HTTP协议：简单对象访问协议，对应于应用层  ，HTTP协议是基于TCP连接的  
tcp协议：    对应于传输层  
ip协议：     对应于网络层   
TCP/IP是传输层协议，主要解决数据如何在网络中传输；而HTTP是应用层协议，主要解决如何包装数据。    
Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。  
http连接：http连接就是所谓的短连接，即客户端向服务器端发送一次请求，服务器端响应后连接即会断掉；  
socket连接：socket连接就是所谓的长连接，理论上客户端和服务器端一旦建立起连接将不会主动断掉；但是由于各种环境因素可能会是连接断开，比如说：服务器端或客户端主机down了，网络故障，或者两者之间长时间没有数据传输，网络防火墙可能会断开该连接以释放网络资源。所以当一个socket连接中没有数据的传输，那么为了维持连接需要发送心跳消息~~具体心跳消息格式是开发者自己定义的  
我们已经知道网络中的进程是通过socket来通信的，那什么是socket呢？socket起源于Unix，而Unix/Linux基本哲学之一就是“一切皆文件”，都可以用“打开open –> 读写write/read –> 关闭close”模式来操作。我的理解就是Socket就是该模式的一个实现，socket即是一种特殊的文件，一些socket函数就是对其进行的操作（读/写IO、打开、关闭），这些函数我们在后面进行介绍。我们在传输数据时，可以只使用（传输层）TCP/IP协议，但是那样的话，如果没有应用层，便无法识别数据内容，如果想要使传输的数据有意义，则必须使用到应用层协议，应用层协议有很多，比如HTTP、FTP、TELNET等，也可以自己定义应用层协议。WEB使用HTTP协议作应用层协议，以封装HTTP文本信息，然后使用TCP/IP做传输层协议将它发到网络上。  
1)Socket是一个针对TCP和UDP编程的接口，你可以借助它建立TCP连接等等。而TCP和UDP协议属于传输层.  
而http是个应用层的协议，它实际上也建立在TCP协议之上。  
(HTTP是轿车，提供了封装或者显示数据的具体形式；Socket是发动机，提供了网络通信的能力。)  
2）Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。Socket的出现只是使得程序员更方便地使用TCP/IP协议栈而已，是对TCP/IP协议的抽象，从而形成了我们知道的一些最基本的函数接口。
### 5.什么是TCP连接的三次握手
第一次握手：客户端发送syn包(syn=j)到服务器，并进入SYN_SEND状态，等待服务器确认；  
第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态；  
第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。  
握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP 连接都将被一直保持下去。断开连接时服务器和客户端均可以主动发起断开TCP连接的请求，断开过程需要经过“四次握手”（过程就不细写了，就是服务器和客户端交互，最终确定断开）
### 6.利用Socket建立网络连接的步骤
建立Socket连接至少需要一对套接字，其中一个运行于客户端，称为ClientSocket ，另一个运行于服务器端，称为ServerSocket 。  
套接字之间的连接过程分为三个步骤：服务器监听，客户端请求，连接确认。    
1.服务器监听：服务器端套接字并不定位具体的客户端套接字，而是处于等待连接的状态，实时监控网络状态，等待客户端的连接请求。  
2.客户端请求：指客户端的套接字提出连接请求，要连接的目标是服务器端的套接字。为此，客户端的套接字必须首先描述它要连接的服务器的套接字，指出服务器端套接字的地址和端口号，然后就向服务器端套接字提出连接请求。  
3.连接确认：当服务器端套接字监听到或者说接收到客户端套接字的连接请求时，就响应客户端套接字的请求，建立一个新的线程，把服务器端套接字的描述发给客户端，一旦客户端确认了此描述，双方就正式建立连接。而服务器端套接字继续处于监听状态，继续接收其他客户端套接字的连接请求。
### 7.TCP/IP通信建立的过程怎样，端口有什么作用
三次握手，确定是哪个应用程序使用该协议
### 8.进程和线程的差别
线程是指进程内的一个执行单元,也是进程内的可调度实体.(线程是进程的基本单位)  
与进程的区别:  
(1)调度：线程作为调度和分配的基本单位，进程作为拥有资源的基本单位  
(2)并发性：不仅进程之间可以并发执行，同一个进程的多个线程之间也可并发执行  
(3)拥有资源：进程是拥有资源的一个独立单位，线程不拥有系统资源，但可以访问隶属于进程的资源.  
(4)系统开销：在创建或撤消进程时，由于系统都要为之分配和回收资源，导致系统的开销明显大于创建或撤消线程时的开销。
```
1，进程：子进程是父进程的复制品。子进程获得父进程数据空间、堆和栈的复制品。  
2，线程：相对与进程而言，线程是一个更加接近与执行体的概念，它可以与同进程的其他线程共享数据，但拥有自己的栈空间，拥有独立的执行序列。  
两者都可以提高程序的并发度，提高程序运行效率和响应时间。  
线程和进程在使用上各有优缺点：线程执行开销小，但不利于资源管理和保护；而进程正相反。
```
### 9.简述多线程的作用以及什么地方会用到多线程?OC实现多线程的方法有哪些?谈谈多线程安全问题的几种解决方案?何为线程同步,如何实现的?分线程回调主线程方法是什么,有什么作用?
```
(1)、多线程的作用:可以解决负载均衡问题,充分利用cpu资源 。为了提高CPU的使用率,采用多线程的方式去同时完 成几件事情而互不干扰,  
(2)、大多情况下,要用到多线程的主要是需要处理大量的IO操作时或处理的情况需要花大量的时间等等,比如:读写文 件、视频图像的采集、处理、显示、保存等。   
(3)、ios有三种主要方法:1、NSThread。2、NSOperation。3、GCD。  
(4)解决方案:使用锁:锁是线程编程同步工具的基础。锁可以让你很容易保护代码中一大块区域以便你可以确保代码的正 确性。使用POSIX互斥锁;使用NSLock类;使用@synchronized指令等。  
(5)回到主线程的方法:    
dispatch_async(dispatch_get_main_queue(), ^{ });作用:主线程是显示UI界面,子线程多数是进行数据处理```
### 10.http和scoket通信的区别?socket连接相关库,TCP,UDP的连接方法,HTTP的几种常用方式? http和scoket通信的区别
http是客户端用http协议进行请求,发送请求时候需要封装http请求头,并绑定请求的数据,服务器一般有web服务器配 合(当然也非绝对)。 http请求方式为客户端主动发起请求,服务器才能给响应,一次请求完毕后则断开连接,以节省资 源。服务器不能主动给客户端响应(除非采取http长连接技术)。iphone主要使用类是NSUrlConnection。 scoket是客户端跟服务器直接使用socket“套接字”进行连接,并没有规定连接后断开,所以客户端和服务器可以保持连 接通道,双方都可以主动发送数据。一般在游戏开发或股票开发这种要求即时性很强并且保持发送数据量比较大的场合使 用。主要使用类是CFSocketRef。 UDP:是用户数据报协议:主要用在实时性要求高以及对质量相对较弱的地方,但面对现在高质量的线路不是容易丢包除非 是一些拥塞条件下 ,如流媒体 TCP:是传输控制协议:是面连接的,那么运行环境必然要求其可靠性不可丢包有良好的拥塞控制机制如http ftp telnet 等  
http的常用方式:get,post
### 11.你连接服务器用的是什么方法,如果请求过程中,网络出了问题这么办?
NSUrlConnection 连接后,有一系列委托方法来接受来自服务器的响应和数据,其中接受相应的方法回得到服务器要传 回的数据有多大,接受数据的方法会反复调用来不断接受服务器数据,如果网络出了问题了,会调用一个方法让你来做相关处理。
### 12.NSOperation queue?
答案：存放NSOperation的集合类。  
操作和操作队列，基本可以看成java中的线程和线程池的概念。用于处理ios多线程开发的问题。  
网上部分资料提到一点是，虽然是queue，但是却并不是带有队列的概念，放入的操作并非是按照严格的先进现出。  
这边又有个疑点是，对于队列来说，先进先出的概念是Afunc添加进队列，Bfunc紧跟着也进入队列，Afunc先执行这个是必然的，但是Bfunc是等Afunc完全操作完以后，B才开始启动并且执行，因此队列的概念离乱上有点违背了多线程处理这个概念。  
但是转念一想其实可以参考银行的取票和叫号系统。  
因此对于A比B先排队取票但是B率先执行完操作，我们亦然可以感性认为这还是一个队列。  
但是后来看到一票关于这操作队列话题的文章，其中有一句提到“因为两个操作提交的时间间隔很近，线程池中的线程，谁先启动是不定的。”  
瞬间觉得这个queue名字有点忽悠人了，还不如pool~  
综合一点，我们知道他可以比较大的用处在于可以帮组多线程编程就好了。
### 13.死锁
**在主线程里面执行同步操作**
### 14.AFN/SDWebImage
### 15.同步锁
多个线程同时修改一个资源需要加锁（++i 不需要，i++需要）
### 16.GCD和NSOperation   
NSOperation:                         可以方便的对线程进行管理
             1.         可以设置最大线程数
          2.可以停止线程         
3.设置线程间的依赖关系 GCD：一旦线程开始，就无法停止，而且不能统一管理


# 三、Other
### 1.如何将产品进行多语言发布?
程序国际化;比如:本地化应用程序名称(1、选中工程,Info—Localizations点击“+”添加要国际化的语言。   
(2、在InfoPlist.strings右边会多出一个三角形,点击展开可看到InfoPlish.strings(english)和InfoPlish.strings(chinese)两个版本的文件;   
(3、在InfoPlish.strings(english)文件中加入:
CFBundleDisplayName ="Program"; 其中“Program”为英文应用程序名称,同理在InfoPlish.strings(chinese)文件中加入: CFBundleDisplayName ="应用程序"; 其中“应用程序”为中文名称,注意:CFBundleDisplayName加不加双引号都行;(4、编辑Info.plist,添加一个新的属性Application has localized display name, 设置其类型为boolean,并将其 value设置为YES即可。

### 2.什么是动态链接库和静态链接库?调用一个类的静态方法需不需要release?
静态连接库就是把(lib)文件中用到的函数代码直接链接进目标程序,程序运行的时候不再需要其它的库文件;动态链接就是 把调用的函数所在文件模块 (DLL)和调用函数在文件中的位置等信息链接进目标程序,程序运行的时候再从DLL中寻找 相应函数代码,因此需要相应DLL文件的支持。 静态链接库和动态链接库的另外一个区别在于静态链接库中不能再包含其他的动态链接库或者静态库,而在动态链接库中还 可以再包含其他的动态或静态链接库。动态的是:运行时才加载到内存中,静态:编译时就加载到内存中 静态方法也就是类方法,不需要release

### 3.什么是push?远程推送
第一步:UIApplication向APNS注册push notification服务1、应用程序 要支持 推送服务(在网页里配置)   
(1)https://developer.apple.com/devcenter/ios/index.action(2)登录 苹果开发者账号(注意是收费账号,$99或$299)   
(3)下载push证书(主要是给程序签名,push服务只有收费开发者才具备。所以需要签名验证),如果没有 push证书,创建一个push证书(App ID->钥匙串程序生成request->push证书)注意事项:App ID的 Bundle ID必须和程序plist文件里的Bundle identifier一致。App ID一旦生成,将不可修改。   
(4)把证书安装到钥匙串里(双击证书文件)(5)生成 编译程序 用的描述文件(网页里进行)  
2、向APNS注册push服务(UIApplication的registerForRemoteNotificationTypes:方法)第二步 获取APNS分配的DeviceToken(64位16进制串)
```objc- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
```第三步 把DeviceToken发送给自己的后台服务器,服务器记录每台设备的DeviceToken以便日后推送信息给客 户端。(需要有一个网络接口,让客户端发送DeviceToken)第四步 服务器推送信息给客户端   
1、服务器除了需要有客户端的DeviceToken之外,还要有push证书,对push的内容进行签名。(苹果为了防 止 恶意向客户端(比如DeviceToken泄露了)发送消息,每次推送消息,都需要证书进行签名,从而避免黑客恶 意攻击用户手机。)   
2、如果你的服务器是java写的,可以直接使用钥匙串导出的p12文件(证书和密钥一起导出)。如果你的服务器 是php写的,因为php语言不支持p12文件类型,需要转换为pem文件。3、将p12转换为pem文件:终端 先找到你p12所在的目录 openssl pkcs12 -in CertificateName.p12 - outCertificateName.pem -nodes4、服务器发送信息给APNS,APNS自动将信息推送给客户端第五步 客户端处理收到的信息
```objc- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
```注意事项1、测试版的push证书仅仅用于开发期间测试,发布版的程序需要生成一个发布版的push证书。 2、测试版APNS的ssl地址和发布版的ssl地址不同 3、测试版的DeviceToken和发布版的DeviceToken也不同4、php文件要喝pem文件放在同一目录。5、除了alert sound和badge之外,json串里还可以包含自定义信息。6、推送的信息最大255字节 7、推送的信息受网络影响较大,有可能造成延迟甚至丢失,重要信息的传递不应该使用push通知,应该有专门的 后台接口。
8、借助push推送,两个客户端可以实现即时通信,工程里面存放我们的p12文件,客户端自己组织json串,发 送请求到APNS。
### 4.iphone阅读器,如果要读取一个文本文件,请问你是如何处理编码问题的?另外像pdf格式的文件,你如何读取?
首先检测文本编码格式(只需读取小部分用来判断), iphone手机阅读器中对于PDF格式的阅读,可以直接用UIWebView控件显示,也可以从网上下到很多直接读取pdf格式 的代码直接从pdf中得到数据。复杂表格动画
```objc- (void)insertRowsAtIndexPaths:(NSArray *)indexPaths withRowAnimation:(UITableViewRowAnimation)animation;- (void)deleteRowsAtIndexPaths:(NSArray *)indexPaths withRowAnimation: (UITableViewRowAnimation)animation;  
- (void)reloadRowsAtIndexPaths:(NSArray *)indexPaths withRowAnimation:(UITableViewRowAnimation)animation;
```### 5.iphone app为什么会被打回来,如何制止?
app的设置界面、按钮使用了类似iphone的操作方式以及icon的圆角设计 -> 重新设计⋯  app的年龄设置太低 -> 改了年龄⋯  app里有实物奖励 -> 免责声明,和苹果无关⋯  app描述里提了后续版本的功能的字样 -> 删除⋯  app有打分的功能 -> 有reject的,也有通过的⋯  app需要使用location,没有提示用户 -> 加了提示,允许用户拒绝⋯  app没提供测试账号 -> 提供⋯app里有私有api -> 修改⋯ 应用内含有有某公司LOGO的图片,没有该公司授权文件,被拒-> 修改⋯第三方静态库包含私有api的调用(联系第三方技术支持,更新静态库); 包含潜在的色情,暴力等内容(调整应用年龄限制等级,并加入举报功能) 做浏览器的,分级必须选17+

### 6.iphone应用程序的项目基本结构?
· Classes -> 源程序文件(.h、.m)· Other Sources-> main.m 等,不需要程序员修改 -Prefix.pch· Resources -> 界面文件(.xib)、配置文件-info.plist· Frameworks -> 链接的库· Targets -> 项目的不同Target(资源、编译配置不同)· Executables -> 项目中所有的可执行文件 -Prefix.pch:_Prefix为所有的项目程序文件预先配置运行环境的前缀标头,在程序运行之前,引入所需框架中的(.h)头文 件。这样可以减少每个头文件对程序编译做出相同的定义,在巨型的应用程序项目开发中节省大量的时间,例如,程序有 100个根文件需要定义abc.h,只需要在_Preix.pch文件下建立一个对象,所有的根文件便可以重复地对程序编译做出定 义。

### 7.ViewController的loadView,viewDidLoad,viewWillAppear,viewDidUnload,dealloc、init分别是在什么时候调用的?在自定义ViewController的时候这几个函数里面应该做什么工作?
1、viewDidLoad 此方法只有当view从nib文件初始化的时候才被调用   
2、viewDidUnload当系统内存吃紧的时候会调用该方法,在该方法中将所有IBOutlet(无论是property还是实例变量) 置 为nil(系统release view时已经将其release掉了)在该方法中释放其他与view有关的对象、其他在运行时创建(但 非系统必须)的对象、在viewDidLoad中 被创建的对象、缓存数据等 release对象后,将对象置为nil(IBOutlet只需要 将其置为nil,系统release view时已经将其release掉了) dealloc方法,viewDidUnload和dealloc方法没有关联,dealloc还是继续做它该做的事情流程应该是这样:(loadView/nib文件)来加载view到内存 -->viewDidLoad函数进一步初始化这些view -->内存不足时,调用 viewDidUnload函数释放views -->当需要使用view时有回到第一步如此循环   
4、viewWillAppear方法,视图即将过渡到屏幕上时调用,(一般在返回需要刷新页面时,我都选择使用代理,所以很少用到)   
5、viewWillDisappear方法,这个A->B之后,A在B之后的操作
### 8.描述程序启动的顺序
1、main.m是程序的入口2、UIApplicationMain()创建应用程序对象,并且为此对象指定委托,检测程序的执行,同时开启事件循环,处理程序接收到的事 件3、UIApplicationDelegate方法的执行 
4、加载window5、指定根视图控制器 6、在指定的试图控制器中添加控件,实现应用程序界面
### 9.描述下拉刷新的实现机制?
**获取数据,刷新页面**

### 10.简述应用程序按Home键进入后台时的生命周期,和从后台回到前台时的生命周期?
```objc
应用程序:  
-[AppDelegate application:willFinishLaunchingWithOptions:]  
-[AppDelegate application:didFinishLaunchingWithOptions:]  
-[AppDelegate applicationDidBecomeActive:]   
退到后台:  
-[AppDelegate applicationWillResignActive:]   
-[AppDelegate applicationDidEnterBackground:]   
回到前台:  
-[AppDelegate applicationWillEnterForeground:]   
-[AppDelegate applicationDidBecomeActive:]   
ViewController之间加载页面:  
-[mainViewController viewDidLoad]   
-[mainViewController viewWillAppear:]   
-[mainViewController viewWillLayoutSubviews]   
-[mainViewController viewDidLayoutSubviews]   
-[mainViewController viewDidAppear:]   
退出当前页面:  
-[mainViewController viewWillDisappear:]  
-[mainViewController viewDidDisappear:]  
返回之前页面:  
-[mainViewController viewWillAppear:]  
-[mainViewController viewWillLayoutSubviews]  
-[mainViewController viewDidLayoutSubviews]  
-[mainViewController viewDidAppear:]
```
### 11.简述值传递和引用传递的区别?
所谓值传递,就是说仅将对象的值传递给目标对象,就相当于copy;系统将为目标对象重新开辟一个完全相同的内存空间。 所谓引用传递,就是说将对象在内存中的地址传递给目标对象,就相当于使目标对象和原始对象对应同一个内存存储空间。此时,如 果对目标对象进行修改,内存中的数据也会改变。

### 12.说说如何进行后台运行程序?
答:判断是否支持多线程UIDevice* device = [UIDevice currentDevice];BOOL backgroundSupported = NO;if ([device respondsToSelector:@selector(isMultitaskingSupported)])backgroundSupported = device.multitaskingSupported; 声明你需要的后台任务Info.plist中添加UIBackgroundModes键值,它包含一个或多个string的值,包括audio:在后台提供声音 播放功能,包括音频流和播放视频时的声音 location:在后台可以保持用户的位置信息 voip:在后台使用VOIP功能 前面的每个value让系统知道你的应用程序应该在适当的时候被唤醒。例如,一个应用程序,开始播放音乐,然后移动到后台仍然需要 执行时间,以填补音频输出缓冲区。添加audio键用来告诉系统框架,需要继续播放音频,并且可以在合适的时间间隔下回调应用程 序;如果应用程序不包括此项,任何音频播放在移到后台后将停止运行。除了添加键值的方法,IOS还提供了两种途径使应用程序在后 台工作:Task completion—应用程序可以向系统申请额外的时间去完成给定的任务Local notifications—应用程序可以预先安排时间执行local notifications 传递实现长时间的后台任务:应用程序可以请求在后台 运行以实现特殊的服务。这些应用程序并不连续的运行,但是会被系统框架在合适的时间唤醒,以实现这些服务
### 13.简述你对UIView、UIWindow和CALayer的理解
UIView:属于UIkit.framework框架,负责渲染矩形区域的内容,为矩形区域添加动画,响应区域的触摸事件,布局和管 理一个或多个子视图  
UIWindow:属于UIKit.framework框架,是一种特殊的UIView,通常在一个程序中只会有一个UIWindow,但可以手 动创建多个UIWindow,同时加到程序里面。UIWindow在程序中主要起到三个作用:1、作为容器,包含app所要显示的所有视图2、传递触摸消息到程序中view和其他对象3、与UIViewController协同工作,方便完成设备方向旋转的支持CAlayer:属于QuartzCore.framework,是用来绘制内容的,对内容进行动画处理依赖与UIView来进行显示,不能处 理用户事件。UIView和CALayer是相互依赖的,UIView依赖CALayer提供内容,CALayer依赖UIView一共容器显示 绘制内容。延伸: UIViewController:管理视图的几成熟,每个视图控制器都有一个自带的视图,并且负责这个视图相关的一切事务。方便 管理视图中的子视图,负责model与view的通信;检测设备旋转以及内存警告;是所有视图控制类的积累,定义了控制器 的基本功能。UIResponder的那张图
1.CALayer                                                                      负责内容的显示，渲染  
2.CALayer不响应触摸事件        
### 14.frame和bounds有什么不同
答案:frame指的是：该view在父view坐标系统中的位置和大小。（参照点是父亲的坐标系统）  
bounds指的是：该view在本身坐标系统中 的位置和大小。（参照点是本身坐标系统）
### 15.position/anchorPoint
position:决定layer在父视图的位置  
anchorPoint：决定position在本身的位置，anchorPoint取值范围：（0，1），默认（0.5，0.5）；
### 16.What is lazy loading?
答案：懒汉模式，只在用到的时候才去初始化。  
也可以理解成延时加载。  
我觉得最好也最简单的一个列子就是tableView中图片的加载显示了。  
一个延时载，避免内存过高，一个异步加载，避免线程堵塞。

### 17.app省电技巧
1.后台定位不要太频繁
2.蓝牙作为周边设备,进入后台后广播频率降低
3.尽可能降低CPU使用率,线程及时进入休眠
### 18.APP升级
Documents                            iCloud    
Library               Library/Caches               tmp                        app升级时,Document,Library不会被覆盖。应该将app版本号保存 在本地。1. 如果启动发现版本号比本地版本号大,则弹出引导页2. 正式进入app之后,跟新本地存储的app版本号
### 19.block1.全局block:如果没有用到局部变量就是全局block,对其copy不会有影响2.栈block:如果用到局部变量,就是栈block。arc下没有栈block
3.堆block:如果对栈block copy,就会变成堆block
---
title: 使用NSURL读写字符串
date: 2016-07-02 10:32:02
tags:
categories: OC
---

## 三、使用NSURL读写字符串
* __1、NSURL简介__

* __什么是URL?__
 * URL的全称是Uniform Resource Locator(统一资源定位符)。
 * URL是互联网上标准资源的地址。
 * 互联网上的每个资源都有一个唯一的URL,它包含的信息指出资源的位置，根据一个URL就能找到唯一的一个资源。

* __URL的格式?__
 * 基本URL包含协议、主机域名(服务器名称\IP地址)、路径
 * 可以简单认为: URL == 协议头://主机域名/路径

* __常见的URL协议头(URL类型)__
* http\https:超文本传输协议资源, 网络资源
  * 在URL前加https://前缀表明是用SSL加密的。你的电脑与服务器之间收发的信息传输将更加安全。

  * Web服务器启用SSL需要获得一个服务器证书并将该证书与要使用SSL的服务器绑定。
  * http和https使用的是完全不同的连接方式,用的端口也不一样,前者是80,后者是443。http的连接很简单,是无状态的。

 * https协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议要比http协议安全。
* __ftp__ :文件传输协议。

* __file__:本地电脑的文件。

* __2、URL创建__
* 通过URL读取字符串

	```objc
	    // 获取URL
	    NSURL *url = [NSURL URLWithString:@"file:///Users/sleepingsun/Desktop/writeFile.txt"];
	
	//    NSURL *url = [NSURL URLWithString:@"http://www.baidu.com"];
	
	   // 保存错误信息
	    NSError *error = nil;
	
	    // 读取URL中的内容
	    NSString *str = [NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:&error];
	    if (error != nil) {
	        // 如果读取失败，打印失败信息
	        NSLog(@"错误信息：%@",error.localizedDescription);
	    }else{
	        // 否则，输出URL内容
	        NSLog(@"%@",str);
	    }
	```

* 通过文件路径创建URL(默认就是file协议的)

	```objc
	    NSURL *url = [NSURL fileURLWithPath:@"/Users/sleepingsun/Desktop/writeFile.txt"];
	    // 读取URL中的内容
	    NSString *str = [NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
	
	    NSLog(@"成功：%@",str);
	// 注意：默认是file协议，不能手动写。否则解析不出来。
	```
#####3、NSString的存储
* 可以将NSString存储到一个文件中

	```objc
	NSString  *str = @"sayHi~";
	// 存储到文件中
	[str writeToFile:@"/Users/sleeping/Desktop/str.txt" atomically:YES encoding:NSUTF8StringEncoding error:nil];
	
	// 存储到URL中
	[str writeToURL:[NSURL URLWithString:@"file:///Users/sleeping/Desktop/str.txt"] atomically:YES encoding:NSUTF8StringEncoding error:nil];
	
	```



---
layout: post
title: "代理、通知、block的比较"
date: 2016-02-25 12:39:16 +0800
comments: true
categories: iOS
---
>代理、通知、block三者在iOS编程中是较为常见的回调模式，三者的用法也有诸多类似之处，但是在具体场景中的选择对于初学者或者研究不够深入的同学来说是比较“随性”的，本文就总结归纳一下三者的用法，以及三者的区别。

<!--more-->

###代理模式
代理模式包含三个方面：1、委托方；2、代理方；3、两者之间的协议；三者之间的关系如下图
![代理模式关系图](http://7xopon.com1.z0.glb.clouddn.com/2016022501.png)  
一般情况下，我们把协议与委托方放在一块。可以这样去理解，有一些需求、或者有一些数据在委托方没有办法得到，只能通过第二方提供，当然也可以独立。

具体代码：数据传递、事件处理
	
	//协议部分
	@protocol CallBackDelegate <NSObject>

	- (void)showArrayWithDelegate:(NSArray*)dataArray;

	@end
	
	//委托方
	@interface CallBackProtocol : NSObject

	@property (nonatomic,weak) id<CallBackDelegate> delegate;

	@end
	
	//代理方
	//1、实现代理的指向，即将CallBackDelegate指向代理方，一般形式
	//self.delegate = 委托方.delegate
	
	//2、实现具体的代理方法
	- (void)showArrayWithDelegate:(NSArray*)dataArray{
	//代理的事情
		_dataArray = dataArray;
	}
	
###通知模式
通知是一种一对多的数据传递、或者事件响应模式，主要需要设置两个方面，1、观察者；2、发送通知方；通过推送中心进行消息转发
![通知模式](http://7xopon.com1.z0.glb.clouddn.com/2016022502.png)  
实现的步骤：1、注册观察者 2、接收通知 3、移除观察者
	
具体代码：数据传递、事件处理

	//注册观察者
	- (void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:animated];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(resiveNotification:) name:@"resiviXXXNotification" object:nil];
    }
    
    //发送消息
     [[NSNotificationCenter defaultCenter] postNotificationName:@"resiviXXXNotification" object:nil];
     
     //移除观察者
     - (void)viewWillDisappear:(BOOL)animated{
    [[NSNotificationCenter defaultCenter] removeObserver:self name:@"resiviXXXNotification" object:nil];
	}
	
###block
相对于delegate或者通知，block相对较为方便。且易读性更强。block可以看成是一个内部函数整体;  
具体代码：
	
	//声明Block
	typedef void(^CallBackBlock)(NSInteger);
	
	//内部方法
	- (void)sendData:(CallBackBlock)block{
		block(3);
	}
	
	//调用函数
	[self sendData:^(NSInteger i){
		_number = i;
	}];
	
需要特别注意的点:block的使用需要注意循环引用的问题  
在ARC下：
	
	__weak typeof(self) weakSelf = self;
	[self sendData:(^NSInteger i){
		[weakSelf  doSomething];
	}];  
在MRC下:

	__block typeof(self) weakSelf = self;
	[self sendData:(^NSInteger i){
		[weakSelf doSomething];
	}];

[block用法大全](http://fuckingblocksyntax.com/)

###三者区别:
1、通知是一种一对多的模式，当有很多的控制器需要知道一个事件，就用通知。但是有一个弊端，当接收的对象多了的话，就有可能存在不想接收通知的对象接收到通知，且需要注意，一定要在dealloc中移除。
2、delegate与block都是一对一的模式，两者的比较：a、block相对要方便，且易读性高；b、delegate运行的成本更低，当block出栈的时候需要由栈内存拷贝到堆内存，对象需要增加引用计数，使用完或者block置为nil才移除，delegate只保存一个对象的指针，没有额外的消耗；c、delegate更加适合过程信息的传输,比如想要知道表格是否滑动，滑到了那样的位置，是否结束滑动。而block更加注重结果
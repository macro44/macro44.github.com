---
layout: post
title: "runloop的常见使用"
date: 2015-12-09 16:32:18 +0800
comments: true
categories: iOS高级开发
---
> 在之前就做过倒计时的功能，最近封装了一个倒计时的控件，之前一直是用第三方库，没有仔细的去了解原理，最近刚好有时间，就研究了一下NSTimer这个类，在研究的过程中，就发现了，NSTimer需与NSRunLoop结合起来玩的才溜，于是就有了这一篇，在我之前项目中遇到的runloop使用以及结合相关资料的总结;这是第一部分，接下来会在另一篇博客中讲解runloop的原理，以及我对runloop的理解。


<!--more-->
###用于“暂停”程序
	
	- (void)start{
    	NSLog(@"start new thread …");
    	[NSThread detachNewThreadSelector:@selector(runOnNewThread) toTarget:self withObject:nil];
    	while (!end) {
        	NSLog(@"runloop…");
        ①	[[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
        	NSLog(@"runloop end.");
	    }
    	NSLog(@"ok.");
    
	}
	
	- (void)runOnNewThread{
		NSLog(@"run for new thread …");
		sleep(1);
		② [self performSelectorOnMainThread:@selector(setEnd) withObject:nil waitUntilDone:NO];
		NSLog(@"end.");
	}
	
	- (void)setEnd{
    	end=YES;
	}
	
调用start方法，输出结果为

	2015-12-09 15:42:14.828 Test1[1648:451473] start new thread …
	2015-12-09 15:42:14.829 Test1[1648:451473] runloop…
	2015-12-09 15:42:14.829 Test1[1648:451585] run for new thread …
	2015-12-09 15:42:15.829 Test1[1648:451585] end.
	2015-12-09 15:43:00.010 Test1[1648:451473] runloop end.
	2015-12-09 15:43:00.010 Test1[1648:451473] ok.
	
解释一下为什么要调用②，因为在调用此方法会向主线程发送消息，唤醒runloop，从而及时响应任务；
  
&nbsp;&nbsp;&nbsp;&nbsp;从上面的代码中不难看出，函数的调用顺序，runloop之前的部分 －> newThread部分 －> runloop之后的部分;这样就相当于帮我们形成了一个线程阻塞，但是while循环不受影响，  
<font color=blue size=3>[常见应用场景:]</font>newThread处理网络请求，runloop前后控制是否显示进度菊花

</br>
###在使用NSTimer的同时使用NSRunloop
①当使用<a href=#2015120901> scheduledTimerWithTimeInterval:invocation:repeats: </a> 和 <a href=#2015120902> scheduledTimerWithTimeInterval:target:selector:userInfo:repeats: </a>创建NSTimer实例的时候，使用的mode为NSDefaultRunLoopMode

②当使用<a href=#2015120903> timerWithTimeInterval:invocation:repeats: </a> 和 <a href=#2015120904> timerWithTimeInterval:target:selector:userInfo:repeats: </a> 创建NSTimer实例的时候需要结合NSRunLoop类的 <a href=#2015120905> addTimer:forMode: </a> 方法将timer添加进runloop

③当使用<a href=#2015120906> initWithFireDate:interval:target:selector:userInfo:repeats: </a> 创建NSTimer实例的时候需要结合NSRunLoop类的 <a href=#2015120905> addTimer:forMode: </a> 方法将timer添加进runloop
	
在某些复杂页面，可能出现NSTimer计时延误的情况，有以下两条思路解决这个问题，1、在子线程中进行NSTimer的操作；2、仍然在主线程中进行NSTimer操作，但是将NSTimer加入到main runloop的特定的mode中；  
</br>
方法1：  

	 if (self.timer) {
         [self.timer invalidate];
         self.timer = nil;
     }
     self.timer = [NSTimer timerWithTimeInterval:0.01 target:self selector:@selector(addTime) userInfo:nil repeats:YES];
     [[NSRunLoop currentRunLoop] addTimer:self.timer forMode:NSRunLoopCommonModes];
     
方法2:  
	
	创建线程
	 NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(newThread) object:nil];
     [thread start];
     
     //在子线程中做的事情 
	-  (void)newThread
	 {
    	 @autoreleasepool
     	{
        	 [NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(addTime) userInfo:nil repeats:YES];
         	[[NSRunLoop currentRunLoop] run];
    	 }
	}
	
方法3: 
 
	//声明全局变量
	 dispatch_source_t _timers;
	
	 
     uint64_t interval = 0.01 * NSEC_PER_SEC;
     dispatch_queue_t queue = dispatch_queue_create("my queue", 0);
     _timers = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
     dispatch_source_set_timer(_timers, dispatch_time(DISPATCH_TIME_NOW, 0), interval, 0);
      __weak ViewController *blockSelf = self;
     dispatch_source_set_event_handler(_timers, ^()
     {
         NSLog(@"Timer %@", [NSThread currentThread]);
         [blockSelf addTime];
     });
     dispatch_resume(_timers);

	- (void)addTime{
		//然后在主线程中修改UI界面：
		 dispatch_async(dispatch_get_main_queue(), ^{
	    	     self.label.text = [NSString stringWithFormat:@"%.2f", self.timeCount/100];
    	 });
     }
	
<b id=2015120901>+ scheduledTimerWithTimeInterval:invocation:repeats:</b>

	+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)seconds
                                 invocation:(NSInvocation *)invocation
                                    repeats:(BOOL)repeats

<b id=2015120902>+ scheduledTimerWithTimeInterval:target:selector:userInfo:repeats: </b>

	+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)seconds
                                     target:(id)target
                                   selector:(SEL)aSelector
                                   userInfo:(id)userInfo
                                    repeats:(BOOL)repeats

<b id=2015120903>+ timerWithTimeInterval:invocation:repeats:</b>

	+ (NSTimer *)timerWithTimeInterval:(NSTimeInterval)seconds
                        invocation:(NSInvocation *)invocation
                           repeats:(BOOL)repeats

<b id=2015120904>+ timerWithTimeInterval:target:selector:userInfo:repeats:</b>

	+ (NSTimer *)timerWithTimeInterval:(NSTimeInterval)seconds
                            target:(id)target
                          selector:(SEL)aSelector
                          userInfo:(id)userInfo
                           repeats:(BOOL)repeats

<b id=2015120905>-  addTimer:forMode:</b>

	- (void)addTimer:(NSTimer *)aTimer
         forMode:(NSString *)mode

<b id=2015120906>- initWithFireDate:interval:target:selector:userInfo:repeats:</b>

	- (instancetype)initWithFireDate:(NSDate *)date
                        interval:(NSTimeInterval)seconds
                          target:(id)target
                        selector:(SEL)aSelector
                        userInfo:(id)userInfo
                         repeats:(BOOL)repeats
                                    
  [NSTimer官方参考文档](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSTimer_Class/index.html#//apple_ref/occ/clm/NSTimer/scheduledTimerWithTimeInterval:invocation:repeats:)                                  
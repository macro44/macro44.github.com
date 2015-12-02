---
layout: post
title: "iOS链式开发(一)"
date: 2015-12-02 16:29:31 +0800
comments: true
categories: iOS高级开发
---

> 烦不烦？当你需要调用一组动画的时候，需要一个个函数组件的去调用，其实你可以运用你之前学过的block，来实现如一系列的连贯触发行为；

###什么是链式开发
简单的说就是通过“.”操作符调用一系列的操作；
<!--more-->

###链式开发在OC中操作的原理
原理：结合类属性的getter属性可以通过.调用的特性，我们可以在每一个需要触发的链式环节，定义一个类属性，然后再类属性中返回类的实例，这样，就可以一系列的执行下去；

如:假设有类ChainInvoking;以及属性`@property （nonatomic, copy）ChainInvoking* invoking;`
则  

	－ （ChainInvoking* ）invoking{
		/**
			我们想做的事情;
		**/
		return self;
	}
	
那么此时，self.invoking与self在意义上其实是相等的，不过在self.invoking已经帮我们做了我们想做的事情，一次类推，如果我们有一系列的动作，那么`self.invoking1.invoking2...`也是可行的，这样就实现了我们的链式调用

###为什么用block而不是直接用类属性
1. 类属性不能传入参数，那么相应的一些功能的时候局限性更大
2. 在最后一个调用的时候，会报警告"Property access result unused-getter should not be used for side effects",意思大概就是获取的getter值，没有实际用途;  
**综上**,最好的办法是使用block 类属性，下面会给大家展示一个例子;

###链式操作的实现
用一个例子说明：   
**ChainedInvoking.h** 
	
	#import <Foundation/Foundation.h>
	@interface ChainedInvoking : NSObject

	@property (nonatomic, copy) ChainedInvoking* (^eat1)();
	@property (nonatomic, copy) ChainedInvoking* (^eat)(NSString* );
	@property (nonatomic, copy) ChainedInvoking* (^run)();
	@property (nonatomic, copy) ChainedInvoking* (^learn)();

	@property (nonatomic, copy) ChainedInvoking* play;
	@property (nonatomic, copy) ChainedInvoking* sing;
	@end

**ChainedInvoking.m**

	#import "ChainedInvoking.h"
	
	@implementation ChainedInvoking

	- (ChainedInvoking* (^)())eat1{  
       return ^{
    	    NSLog(@"吃饭");
    	    return self;
	  };
	}

	- (ChainedInvoking* (^)())learn{
    	return ^{
       	 NSLog(@"学习");
        	return self;
    	};
	}	

	- (ChainedInvoking* (^)())run{
    	return ^{
        	NSLog(@"跑步");
	        return self;
    	};
	}

	- (ChainedInvoking* (^)(NSString* food))eat{
    	return ^(NSString* food){
        	NSLog(@"吃---%@",food);
	        return self;
    	};
	}

	- (ChainedInvoking*)play{
    	NSLog(@"玩耍!");
	    return self;
	}

	- (ChainedInvoking*)sing{
    	NSLog(@"唱歌!");
	    return self;
	}		

	@end
	
**调用:	**
	
	ChainedInvoking* invoking = [[ChainedInvoking alloc] init];
	invoking.eat1().run().learn().eat(@"辣子鸡饭"); ①
	invoking.play.sing.eat(@"红烧牛肉面");	②
	
**结果**  
①
	
	吃饭
	跑步
	学习
	吃-－－辣子鸡饭
②
	
	玩耍
	唱歌
	吃红烧牛肉面
	

###参考文档
[使用OC链式调用方式简化SpriteKit的动画调用](http://www.cocoachina.com/ios/20151123/14317.html)




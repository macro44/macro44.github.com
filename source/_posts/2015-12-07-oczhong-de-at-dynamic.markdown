---
layout: post
title: "OC中的@dynamic"
date: 2015-12-07 16:58:12 +0800
comments: true
categories: iOS基础
---

> 在分析一份代码的时候，遇到了@dynamic，以前也遇到过，但是没有系统的总结一下，刚好有空，系统总结一下，为以后的使用做好准备；

一、@dynamic与@synthesize的区别  

1. @property有两个对应的词，一个是@synthesize,另一个是@dynamic,当两个都没写的时候，系统会默认为写了@property的属性添加@synthesize var=_var;                                   
2. @synthesize的语义为如果你没有重写getter或setter方法，系统会自动帮忙生成getter和setter方法，@dynamic的语义为getter和setter方法由用户自己实现，不自动生成；

<!--more-->

二、@dynamic的读取  
View.h
	
	#import <UIKit/UIKit.h>

	@interface View : UIView
	{
    	NSString * _name;
	}

	@property (nonatomic,copy) NSString *name;

	@end
	
View.m

	
	#import "View.h"

	@implementation View

	@dynamic name;

	- (void)setName:(NSString *)name{
       _name = name;
	}

	- (NSString*)name{
       return _name;
	}
	
	@end
如果没有写setter和getter两个方法，调用的时候，[[View alloc] init].name会报错，但是将@dynamic换成@synthesize的话没有getter和setter也不会有问题；

三、@dynamic的使用 

1. 用于NSManagedObject中，告诉编译器不要处理属性的getter和setter方法，由CoreData框架来生成getter和setter方法
2. 用于分类中,具体使用需要结合到runtime，让分类可以存在属性变量
 例子如下：  
 NSObject + AAA.h
 
 
		#import <Foundation/Foundation.h> 
		@interface NSObject (AAA)  
		
		@property (nonatomic,copy) NSString *aaa;  
		
		@end
		
NSObjct + AAA.m

		#import "NSObject+AAA.h"
		#import <objc/runtime.h>

		static const void *kAAAIdentifier = (void*)@"kAAAIdentifier";

		@implementation NSObject (AAA)

		@dynamic aaa;

		- (void)setAaa:(NSString *)aaa{
				objc_setAssociatedObject(self, kAAAIdentifier, aaa, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
		}

		- (NSString*)aaa{
			return  objc_getAssociatedObject(self, kAAAIdentifier);
		}

		@end

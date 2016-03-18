---
layout: post
title: "关联属性"
date: 2016-03-18 21:40:42 +0800
comments: true
categories: iOS
---
> 在对类的扩展的时候，iOS不能扩展类的存储属性，我们可以用运行时的关联来“扩展”;

<!--more-->

<br/>
###创建关联
objc_setAssociatedObject(object: AnyObject!, _ key: UnsafePointer<Void>, _ value: AnyObject!, _ policy: objc_AssociationPolicy)  
包含四个参数;

- 被关联的对象
- 关键字
- 关联的对象
- 关联的策略

###获取关联属性
objc_getAssociatedObject(object: AnyObject!, _ key: UnsafePointer<Void>) -> AnyObject!

- 被关联的对象
- 关键字

<br/>

###例子
关联属性一般用在对类的扩展中属性值上
####OC版的例子
xxx+xx.h文件  

	@property (nonatomic, strong) NSArray *array;

xxx+xx.m文件
	
	// 定义全局变量
	const void * associateKey = @"associateKey";
	
	- (void)setArray:(NSArray*)array{
		objc_setAssociateObject(self, associateKey, array, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	}
	
	- (NSArray*)array{
		objc_getAssociatedObject(self, associateKey);
	}
    
####Swift版的例子
	// 在extension之外
	let associateKey = "associateKey"
	
	var array:Array<String>{
		set{
			objc_setAssociateObject(self, associateKey, array, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
		}
		get{
		 	objc_getAssociatedObject(self, associateKey);
		}
	}



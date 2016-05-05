---
layout: post
title: "nonnull和nullable"
date: 2016-05-05 17:29:56 +0800
comments: true
categories: iOS
---


###属性的含义与使用
nonnull表示的意思是属性应该非空，如果属性赋值NULL或者nil，则提供警告，而nullable表示的意思是对象可以为NULL或者nil

- 在属性中的使用


		@property (nonatomic, copy) NSString* _nonnull items;
		或者
		@property (nonatomic, copy, nonnull) NSString* items;
	
	表示该属性应该不为空，

- 在方法中的使用 


		- (void)createArray:(NSArray* __nonnull) array;
	
	当  [self createArray:nil] 时会有警告;

- nonnull不会影响性能，运行时对象仍然可以为nil；

<br>
###更优的使用方法
当一个类有很多的属性和方法，这个时候，使用nonnull去一个个定义就不太方便了，为此苹果已经在UIFoundation中定义了两个宏
	
	NS_ASSUME_NONNULL_BEGIN
	NS_ASSUME_NONNULL_END
	
表示这两个宏之间所有简单指针多想都被假定为nonnull,因此我们只需要去指定那些nullable就行了。
---
layout: post
title: "BlocksKit的理解(一)"
date: 2016-05-06 14:39:16 +0800
comments: true
categories: iOS
---
>  BlocksKit是Github上一个对常用代理实现block方法的一个开源库，github的地址为:[Blockskit](https://github.com/zwaldowski/BlocksKit)

准备分几个章节来分析学习Blockskit，本问主要对Blockskit的BKDefines.h宏文件进行初步分析,源码以及标注如下所示
	
	// 定义是否有可执行函数
	#ifndef __has_builtin
	#define __has_builtin(x) 0
	#endif
	// 定义是否含有某个头文件
	#ifndef __has_include
	#define __has_include(x) 0
	#endif
	// 定义是否含有某个特点，如arc
	#ifndef __has_feature
	#define __has_feature(x) 0
	#endif
	// 定义是否含有某个属性
	#ifndef __has_attribute
	#define __has_attribute(x) 0
	#endif
	// 定义是否含有某个拓展
	#ifndef __has_extension
	#define __has_extension(x) 0
	#endif
	
继续深入的去看会发现这部分在系统文件 Foundation/NSObjCRuntime.h 里面已经有了后面三个。定义的意义与这里面相符，不过因为使用了ifndef，所以对这不产生影响 
	
	#if !defined(NS_ASSUME_NONNULL_BEGIN)
	# if  __has_feature(assume_nonnull)
	#  define NS_ASSUME_NONNULL_BEGIN _Pragma("clang assume_nonnull begin")
	#  define NS_ASSUME_NONNULL_END   _Pragma("clang assume_nonnull end")
	# else
	#  define NS_ASSUME_NONNULL_BEGIN
	#  define NS_ASSUME_NONNULL_END
	# endif
	#endif
	
这段理解成,如果没有定义NS_ASSUME_NONNULL_BEGIN，则再次判断__has_feature(x)的值，如果为真，则定义NS_ASSUME_NONNULL_BEGIN的值，否则不定义，但是其实这一段没有起到任何作用，因为，如果NS_ASSUME_NONNULL_BEGIN没有定义，其实也不会做任何事情
	
	#if !__has_feature(nullability)
	# define nonnull
	# define nullable
	# define null_unspecified
	# define __nonnull
	# define __nullable
	# define __null_unspecified
	#endif
	
	#if __has_feature(objc_generics)
	#   define __GENERICS(class, ...)      class<__VA_ARGS__>
	#   define __GENERICS_TYPE(type)       type
	#else
	#   define __GENERICS(class, ...)      class
	#   define __GENERICS_TYPE(type)       id
	#endif
	
	#if !defined(BK_INITIALIZER)
	# if __has_attribute(objc_method_family)
	#  define BK_INITIALIZER __attribute__((objc_method_family(init)))
	# else
	#  define BK_INITIALIZER
	# endif
	#endif
	
	#if !defined(BK_ALERT_CONTROLLER_DEPRECATED)
	# define BK_ALERT_CONTROLLER_DEPRECATED(intro) NS_DEPRECATED_IOS(intro, 8_0, "The BlocksKit extensions for UIAlertView and UIActionSheet are deprecated. Use UIAlertController instead.");
	#endif
	
	#if !defined(BK_URL_CONNECTION_DEPRECATED)
	# define BK_URL_CONNECTION_DEPRECATED NS_DEPRECATED(10_5, 10_11, 2_0, 9_0, "The BlocksKit extensions for NSURLConnection are deprecated. Use NSURLSession instead.");
	#endif

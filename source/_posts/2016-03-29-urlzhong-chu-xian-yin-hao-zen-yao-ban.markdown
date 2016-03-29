---
layout: post
title: "url中出现引号怎么办"
date: 2016-03-29 14:55:31 +0800
comments: true
categories: iOS
---

> 经常遇到这样的问题在get/post请求的时候，传的头参数中包含有字符串，而直接使用字符串拼接的话会使字符串参数带有双引号，而由string转url的过程中不允许出现双引号，这个时候我们需要对字符串进行过滤。

<!--more-->

		    NSString *urlString = @"http://xxxx/xx?out_trade_no=\"123\"";
	    	urlString = [urlString stringByReplacingOccurrencesOfString:@"\"" withString:@""];
			NSURL *url = [NSURL URLWithString:urlString];
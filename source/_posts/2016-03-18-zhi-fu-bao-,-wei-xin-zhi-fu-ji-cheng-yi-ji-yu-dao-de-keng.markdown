---
layout: post
title: "支付宝、微信支付集成以及遇到的坑"
date: 2016-03-18 16:25:55 +0800
comments: true
categories: 
---
[支付宝支付集成开发文档](https://doc.open.alipay.com/doc2/detail.htm?spm=a219a.7629140.0.0.oyqOmT&treeId=59&articleId=103676&docType=1)
[微信支付集成开发文档](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=8_5)

具体的集成过程就不详说了，看官方文档基本集成应该没有问题，具体说一下遇到了哪些坑。

<!--more-->

坑1：支付宝集成的时候如果实在本地配置apiKey时需是转化为pkcs8格式，OC版本中间能使用换行符"\"，但swift版本不允许,有两个办法，1、将apikey拷贝到oc版本的系统提供的demo中，然后运行打印，然后copy到swift版本进行粘贴；2、拷贝到文本编辑器中，然后选择去掉中间的空格，然后转化成无格式文本，再粘贴；

坑2： 找不到头文件：#import <openssl/xxx.h>,方法：在target  -> Build Settings -> Search Paths ->Header Search Paths里面，添加 $(PROJECT_DIR)/上层文件夹名/AliPay,至于其他的报常见类找不到，一般需要添加#import <UIKit/UIKit.h>头文件

坑3：微信支付按照流程集成成功后，支付只有一个“确定”按钮，问题原因，参数出错，我的解决办法是参照网上的解决办法，让后台把签名中的sign全部改为大写。

坑4： 微信不能回调,这个问题一定要确保URL Schemes里面的appid与注册时候的appid一致；
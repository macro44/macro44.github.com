---
layout: post
title: "Apple Pay应用内集成"
date: 2016-02-26 19:52:41 +0800
comments: true
categories: 支付
---
>Apple pay于2016年2月18日在国内上线，国内也已经有部分应用开始集成了。未来在电商类的项目中，又多了一个选择。本文主要讲述Apple pay的使用，下一篇将讲述Apple pay与主流的支付方式的区别。

<!--more-->

[转载并整理于博客园"大顺子"博客](http://www.cnblogs.com/dashunzi/p/ApplePay.html)  
[转载并整理于CocoaChina-iOS开发](http://www.cocoachina.com/ios/20141023/10026.html)

--参考资料  
[苹果开发者中心-Apple Pay入门](https://developer.apple.com/apple-pay/get-started/cn/)  
[PassKit Framework](https://developer.apple.com/library/ios/documentation/UserExperience/Reference/PassKit_Framework/index.html#//apple_ref/doc/uid/TP40012158)


###Apple pay目前的设备支持情况
![设备支持图](http://7xopon.com1.z0.glb.clouddn.com/2016022601.png)

可以看到对于iPhone主要是iphone6以上的设备

###Apple Pay目前的系统支持情况

iOS8.0以上，但是需要注意的是直到iOS9.2才真正的支持银联支付

###Apple Pay的集成
1. 配置支付环境、标识符以及相应的证书
	- 创建一个工程，并设置好对应的Bundle ID
	- 注册并配置一个商业标识符（merchant ID）
		* 添加一个App ID,并在App Services中勾选Apple Pay选项
		* 配置一个Merchat ID，并点击Edit将选项勾选为Yes，表示支持美国以外的地区支持支付
		* 下载Merchant ID证书，并且验证有效性
		* 绑定Merchant ID到App ID

2. 配置Xcode中的选项
	- 更改iOS Deployment Target为8.0
	- 在Target -> Capabilities 中开启Apple Pay服务并选择Marchat ID
	
3. 步骤理解以及相应的代码实现
	![](http://7xopon.com1.z0.glb.clouddn.com/2016022702.png)
	
	- 判断当前设备是否可以支付(包含两个部分：1、当前设备的硬件是否支持；2、当前设备的软件环境是否支持)
		
			 // 1、当前设备是否支持Apple Pay
		    if (![PKPaymentAuthorizationViewController canMakePayments]) {
        		NSLog(@"当前设备不支持Apple Pay支付");
		    }
	- 判断是否已经添加了可以支付的银行卡
			
			// 2、判断是否添加了可用于支付的银行卡
			// PKPaymentNetworkPrivateLabel为储蓄卡/信用卡
			if (![PKPaymentAuthorizationViewController canMakePaymentsUsingNetworks:@[PKPaymentNetworkAmex,PKPaymentNetworkDiscover,PKPaymentNetworkMasterCard,PKPaymentNetworkPrivateLabel,PKPaymentNetworkVisa]]) {
				NSLog(@"没有添加银行卡");
				
				// 2.1 当没有银行卡的时候创建按钮
        		PKPaymentButton *payButton = [PKPaymentButton buttonWithType:PKPaymentButtonTypeSetUp style:PKPaymentButtonStyleWhiteOutline];
        		[payButton addTarget:self action:@selector(addCard:) forControlEvents:UIControlEventTouchUpInside];
        		//将payButton添加到页面的指定位置上	
			}else{
        		// 2.2 当有银行卡的时候创建按钮
        		PKPaymentButton *payButton = [PKPaymentButton buttonWithType:PKPaymentButtonTypeBuy style:PKPaymentButtonStyleBlack];
	        	[payButton addTarget:self action:@selector(pay:) forControlEvents:UIControlEventTouchUpInside];
	        	//将payButton添加到页面的指定位置上	
    		}
    		
    		// 2.3 没有银行卡时跳转至添加银行卡页面
    		- (void)addCard:(PKPaymentButton*)button{
    			 PKPassLibrary *passLibrary = [[PKPassLibrary alloc] init];
    			 if ([passLibrary isPaymentPassActivationAvailable]) {
    			 	[passLibrary openPaymentSetup];
			    }	
    		}
    		
    		// 2.4 有银行卡时跳转至支付页面
    		- (void)pay:(PKPaymentButton*)button{
    			//创建支付请求并进行授权处理
    		}
			
	- 创建一个支付请求，并配置各项信息
	
		    // 1、创建一个支付请求
		    PKPaymentRequest *request = [[PKPaymentRequest alloc] init];
    
		    // 1.1 创建支付请求
		    // 1.1.1 配置 merchant id
		    request.merchantIdentifier = @"xxx";
    
		    // 1.1.2 配置货币信息以及国家信息
		    request.countryCode = @"CN";
		    request.currencyCode = @"CNY";
    
		    // 1.1.3  配置请求支持的卡片类型
		    request.supportedNetworks = 	@[PKPaymentNetworkChinaUnionPay,PKPaymentNetworkVisa];
    
		    // 1.1.4 配置商家验证方式
		    request.merchantCapabilities = PKMerchantCapability3DS;
    
		    // 1.1.5 配置商品列表(需要注意的点是paymentSummaryItems数组的最后一个成员应该为总额)
		    NSDecimalNumber *price = [NSDecimalNumber decimalNumberWithString:@"12.6"];
		    PKPaymentSummaryItem *item = [PKPaymentSummaryItem summaryItemWithLabel:@"苹果" amount:price];
    
		    NSDecimalNumber *totalPrice = [NSDecimalNumber decimalNumberWithString:@"12.6"];
		    PKPaymentSummaryItem *totalItemCost = [PKPaymentSummaryItem summaryItemWithLabel:@"总额" amount:totalPrice];
		    request.paymentSummaryItems = @[item,totalItemCost];
    
		    // 1.2 配置请求的附加项
		    // 1.2.1 配置发票的收获地址
		    request.requiredBillingAddressFields = PKAddressFieldAll;
    
    
		    // 1.2.2 是否显示收获地址
		    request.requiredShippingAddressFields = PKAddressFieldAll;
    
		    // 1.2.3 配置快递方式
		    NSDecimalNumber *number = [NSDecimalNumber decimalNumberWithString:@"12.0"];
		    PKShippingMethod *method = [PKShippingMethod summaryItemWithLabel:@"顺风快递" amount:number];
		    method.identifier = @"shunfeng";
		    method.detail = @"当日可达";
		    request.shippingMethods = @[method];

			
	- 弹出授权控制器，让用户进行支付授权
	
		    // 弹出用户支付授权页面
		    PKPaymentAuthorizationViewController *authCtrl = [[PKPaymentAuthorizationViewController alloc] initWithPaymentRequest:request];
		    authCtrl.delegate = self;
		    [self presentViewController:authCtrl animated:YES completion:nil];
	
	- 处理支付凭证，以及弹出的模态控制器的处理
		
			//支付凭证是一个需要由服务器参与的过程，苹果通过产生支付凭证，发送给服务器，
			服务器对凭证进行处理，处理完之后将支付状态返回给客户端，让客户端进行处理。
			
		![凭证产生图](http://7xopon.com1.z0.glb.clouddn.com/2016022701.png)
		
			//具体的返回地方实在委托当中
			// 授权成功调用此方法
			- (void)paymentAuthorizationViewController:(PKPaymentAuthorizationViewController *)controller
                       didAuthorizePayment:(PKPayment *)payment
                                completion:(void (^)(PKPaymentAuthorizationStatus 			status))completion{
                                //服务器处理结果的过程
    							//payment中有一个token，包含支付信息，发送给服务器，
    								服务器进行相应的处理后返回一个支付状态，然后客户端进行
    								处理；
    							//可以对支付状态进行匹配。包含在PKPaymentAuthorizationStatus中。
			}
			
			// 授权是否结束，包括授权finish，或者取消授权，对控制器的处理
			- (void)paymentAuthorizationViewControllerDidFinish:(PKPaymentAuthorizationViewController *)controller{
				NSLog(@"授权完成");
			    [self dismissViewControllerAnimated:YES completion:nil];
			}
4. 服务器处理
	
	- 验证支付数据的哈希表和签名 
	- 为加密过的支付数据解码 　
	- 向支付处理系统提交支付数据 
	- 向订单追踪系统提交订单   
处理支付请求时，你有两个选择；你既可以利用支付平台处理支付请求，也可以自己实现支付请求处理流程。一个常用的支付平台可以完成上述大部分操作。

　　　关于支持Apple Pay支付平台的更多信息，请参考[developer.apple.com/apple-pay/](https://developer.apple.com/apple-pay/)
　　　

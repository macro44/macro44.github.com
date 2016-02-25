---
layout: post
title: "iOS网络框架ASI与AFN的比较"
date: 2016-02-25 23:08:15 +0800
comments: true
categories: iOS
---

>ASIHttpRequest(ASI)与AFNetworking(AFN)这两个网络框架,对每一个iOS开发者开发新项目要从中选择一个作为应用的主要网络框架都不是一件容易的事情，两者各有优鄙。下面我们将从原理、用法、功能、性能等等方面来总结比较一下这两者；

<!--more-->

###原理
![原理图](http://7xopon.com1.z0.glb.clouddn.com/2016022503.png)
原理图如上图所示  
框架的基础都是基于socket，BSD Socket是一种最常见的Socket抽象接口；  
Core Foundation中的CFSocket就是基于BSD Socket开发的。它几乎涵盖了BSD Socket的全部共功能，更重要的是把Socket整合到事件的处理循环中。Core Foundation中CFStream是基于CFSocket开发的读写流支持；  
CFNetwork是基于CoreFoundation中CFStream的一个底层高性能网络框架，它由提供基础服务的CFSocketStream、支持HTTP协议的CFHTTP、基于CFHTTP用于身份验证的CFHTTPAuthentication和支持FTP协议的CFFTP组成;  
如上图所示，ASI是基于CFHTTP开发的，而AFN是基于NSURL开发的，也就是说ASI比AFN更底层；  

###用法
ASI基本上都是通过代理的方法进行回调，而AFN基本上是通过block

###功能
AFN只封装了一些常用功能，满足基本需求，而忽略了很多扩展功能。例如：AFN默认没有封装同步请求，如果开发者需要使用同步请求，则需要重写getPath:parameters:success:failure方法，对于AFHTTPRequestOperation进行同步处理；而ASI则是直接通过调用一个startSynchronous方法。

另外，AFN针对JSON、XML、Plist和Image四中数据结构封装了各自的处理器，开发者可以把处理器注册到操作队列中，直接在回调方法中获得格式化以后的数据；而ASI在这方面没有进行处理，但预留了接口和工具进行处理。

###性能方面
ASI表现的更加平稳，优异


###总体比较表格
<table STYLE="TexT-ALiGn: left; WiDoWs: 2; TexT-TrAnsForM: none; TexT-inDenT: 0px; FonT: medium Tahoma; WHiTe-spACe: normal; orpHAns: 2; LeTTer-spACinG: normal; CoLor: rgb(0,0,0); WorD-spACinG: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px" BORDER="1" CELLSPACING="0" CELLPADDING="2" WIDTH="100%">
<tbody>
<tr>
<td VALIGN="top"><br />
对比</TD>
<td STYLE="TexT-ALiGn: center" VALIGN="top">ASI</TD>
<td STYLE="TexT-ALiGn: center" VALIGN="top">AFN</TD>
</TR>
<tr>
<td VALIGN="top">更新状态</TD>
<td VALIGN="top">2012年10月份，已经停止更新</TD>
<td VALIGN="top">持续更新中，目前已更新至2.0版</TD>
</TR>
<tr>
<td VALIGN="top">介绍</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: left">
ASI的直接操作对象<b>ASIHTTPRequest，</B>是一个实现了了NSCopying协议的NSOperation子类。</DIV>
<div STYLE="TexT-ALiGn: left">
在initialize和initWithURL:方法中初始化相关属性并配置一系列请求相关参数默认值。此外，ASIHTTPRequest还提供了一系列的实例方法用来配置请求对象。</DIV>
</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
AFN的直接操作对象<b>AFHTTPClient</B>，是一个实现了NSCoding和NSCopying协议的NSObject子类。AFHTTPClient是一个封装了一系列操作方法的“工具类”，处理请求的操作类是一系列单独的，基于NSOperation封装的，AFURLConnectionOperation<wbr>的子类。</TD>
</TR>
<tr>
<td STYLE="TexT-ALiGn: left" VALIGN="top">线程处理模式</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
<div>
每一个请求都由构造方法初始化一个（共享）实例，通过这个实例配置参数并发起请求。ASI最初使用delegate模式回调，在iOS
SDK支持Block之后也提供了注册Block的实例方法。</DIV>
<div>
ASI采取的是CFHTTP请求完成，直接回调ASIHTTPRequest的实例方法，通过储存的实例对象记录的信息完成Delegate模式或Block模式的回调。</DIV>
<div><br /></DIV>
<div>
在异步请求的处理上，ASIHTTPRequest对象初始化结束后，在startAsynchronous方法中把对象加入共享操作队列。此后，<b>包括创建CFHTTPMessageRef，也就是处理网络请求的主要对象（事实上是一个指向__CFHTTPMessage结构的指针），在内的所有操作都在ASIHTTPRequest对象所属的子线程中完成。</B></DIV>
</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: left">
AFN的示例代码中通过一个静态方法，使用dispatch_once()的方式创建AFHTTPClient的共享实例，这也是官方建议的使用方法。在创建AFHTTPClient的初始化方法中，创建了OperationQueue并设置一系列参数默认值。在getPath:parameters:success:failure方法中创建NSURLRequest，以NSURLRequest对象实例作为参数，创建一个NSOperation，并加入在初始化发方中创建的NSOperationQueue。</DIV>
<div STYLE="TexT-ALiGn: left">
<b>以上操作都是在主线程中完成的。在NSOperation的start方法中，以此前创建的NSURLRequest对象为参数创建NSURLConnection并开启连结。</B></DIV>
</TD>
</TR>
<tr>
<td STYLE="TexT-ALiGn: left" VALIGN="top">数据处理模式</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
ASI在这方面显得更原始，没有针对任何数据类型做特别封装，只是预留了各种接口和工具供开发者自行扩展。</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
AFN针对JSON、XML、PList和Image四种数据结构封装了各自处理器，开发者可以把处理器注册到操作队列中，直接在回调方法中获得格式化以后的数据。</TD>
</TR>
<tr>
<td STYLE="TexT-ALiGn: left" VALIGN="top">同步请求</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: left">
ASI则是直接通过调用一个startSynchronous方法。</DIV>
<div STYLE="TexT-ALiGn: left"><br /></DIV>
</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
AFN默认没有封装同步请求，如果开发者需要使用同步请求，则需要重写getPath:parameters:success:failure方法，对AFHTTPRequestOperation进行同步处理</TD>
</TR>
<tr>
<td STYLE="TexT-ALiGn: left" VALIGN="top">异步回调的处理</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: left">
【使用AFNetworking进行网络异步请求时，block:(void(^)代码块实际返回到UI主线程中。<span STYLE="BACKGroUnD-CoLor: rgb(255,255,255)">即使在子线程中使用AFNetWorking进行网络的异步请求，block:(void(^)代码块仍然返回到UI主线程中（<b>AF框架，它里面已经create了异步线程<span>&nbsp;<wbr></SPAN></B>）。因此</SPAN><b STYLE="BACKGroUnD-CoLor: rgb(255,255,255)">无论当前处在主线程还是子线程，异步返回均返回到UI主线程中。】</B></DIV>
</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: left">
为一系列相关的请求定义一个HTTPClient，共用一个BaseURL。每次请求把URL中除BaseURL的Path部分做为参数传给HTTPClient的静态方法，并注册一个Block用于回调。</DIV>
<div STYLE="TexT-ALiGn: left">
AFN则直接使用了NSOperation的completionBlock属性。</DIV>
</TD>
</TR>
<tr>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: left">基于的底层开发框架</DIV>
<div STYLE="TexT-ALiGn: left"><br /></DIV>
</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: center">CFNetwork框架</DIV>
<div STYLE="TexT-ALiGn: left">
使用CFnetwork而不是Cocoa框架NSURL有几点好处。CFNetwork更加专注于网络协议，而NSURL更加专注于数据访问，比如通过HTTP或者FTP传输数据。尽管NSURL的确也提供了一些可配置功能，可是CFNetwork提供的要多的多。另外NSURL还需要你使用Objective_c。如果做不到这点的话，还是应该使用CFNetwork</DIV>
</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: center">NSURL</DIV>
<div STYLE="TexT-ALiGn: left">【使用iOS5.0 SDK NSURLConnection：</DIV>
<div STYLE="TexT-ALiGn: left">
&nbsp;<wbr>1、进行网络同步请求（sendSynchronousRequest）时，调用该请求接口的操作在哪个线程，同步返回的网络结果就处于哪个线程，因此通常进行网络同步请求时，为了避免阻塞UI主线程，需要在子线程中进行网络请求；</DIV>
<div STYLE="TexT-ALiGn: left">
2、进行网络异步请求（sendAsynchronousRequest）时，block:(void(^)代码块实际返回到子线程中。因此，此时如需要向UI线程发送通知，则需要跳转到主线程中发送通知dispatch_async(dispatch_get_main_queue(),
^{}）；】</DIV>
</TD>
</TR>
<tr>
<td STYLE="TexT-ALiGn: left" VALIGN="top">底层开发矿建介绍</TD>
<td VALIGN="top">
<div STYLE="TexT-ALiGn: left">CFNetwork是基于Core
Foundation中CFStream的一个底层高性能网络框架，它由提供基础服务的CFSocketStream，支持HTTP协议的CFHTTP，基于CFHTTP用于身份认证的CFHTTPAuthentication和支持FTP协议的CFFTP组成。</DIV>
<div STYLE="TexT-ALiGn: left">Core Foundation框架中的CFSocket就是基于BSD
Socket开发的。它几乎涵盖了BSD Socket的全部功能，更重要的是把Socket整合到事件的处理循环中。Core
Founda-tion中较高层的CFStream是基于CFSocket开发的读写流支持。</DIV>
</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
如图所示，ASI是基于CFHTTP开发的一个组件；而AFN的基础——NSURL，也是基于CFNetwork开发的，也就是说ASI相比AFN更加底层。</TD>
</TR>
<tr>
<td STYLE="TexT-ALiGn: left" VALIGN="top">性能对比</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">AFN请求优于ASI</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top"><br /></TD>
</TR>
<tr>
<td STYLE="TexT-ALiGn: left" VALIGN="top">总结</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
ASI更适合已经发展了一段时间的应用，或者开发资源相对丰富的团队，因为往往这些团队（或他们的应用）已经积累了一定的经验，无论是产品上还是技术上的。需求复杂度就是在这种时候高起来，而且底层订制的需求也越来越多，此时AFN就很难满足需求，需要牺牲一定的易用性，使用ASI作为网络底层控件。</TD>
<td STYLE="TexT-ALiGn: left" VALIGN="top">
AFN适合逻辑简单的应用，或者更适合开发资源尚不丰富的团队，因为AFN的易用性要比ASI好很多，而这样的应用（或团队）对底层网络控件的定制化要求也非常低。</TD>
</TR>
</TBODY>
</TABLE>
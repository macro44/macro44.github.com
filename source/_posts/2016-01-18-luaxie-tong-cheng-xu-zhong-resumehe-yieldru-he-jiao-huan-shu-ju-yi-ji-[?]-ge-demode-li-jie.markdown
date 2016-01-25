---
layout: post
title: "Lua协同程序中resume和yield如何交换数据以及一个Demo的理解"
date: 2016-01-18 10:45:23 +0800
comments: true
categories: Lua
---
>协同在Lua中的作用和线程比较类似，协同有非常强大的功能，但用起来也很复杂，其中最难理解的一部分就是协同的交换数据部分

先来看一下协同的几个基本函数
<table class="reference">
<tr><th>方法</th><th> 描述</th></tr>
<tr><td>coroutine.create()</td><td>
创建coroutine，返回coroutine， 参数是一个函数，当和resume配合使用的时候就唤醒函数调用</td></tr><tr><td>

coroutine.resume()</td><td>

重启coroutine，和create配合使用</td></tr><tr><td>

coroutine.yield()</td><td>

挂起coroutine，将coroutine设置为挂起状态，这个和resume配合使用能有很多有用的效果</td></tr><tr><td>

coroutine.status()</td><td>

查看coroutine的状态<br>

注：coroutine的状态有三种：dead，suspend，running，具体什么时候有这样的状态请参考下面的程序</td></tr><tr><td>

coroutine.wrap（）</td><td>

创建coroutine，返回一个函数，一旦你调用这个函数，就进入coroutine，和create功能重复</td></tr><tr><td>

coroutine.running()</td><td>

返回正在跑的coroutine，一个coroutine就是一个线程，当使用running的时候，就是返回一个corouting的线程号</td></tr></table>在这些函数中一般出现交换数据的部分在resume函数与yield之间，我们来看几个例子

	--例子1
	local co = coroutine.create(function(i)
             print("arg: ", i)			--①
             print("yield return: ", coroutine.yield(100, i)) --②
             print("print end")			--③	
             return "coroutine end"	--④
             end)
	print("resume 1: ", coroutine.resume(co, 1))  --⑥
	print("resume 2: ", coroutine.resume(co, 2))  --⑦
	
	下面是运行结果
	arg: 	1		--⑧
	resume 1: 	true	100	1	--⑨
	yield return: 	2	--⑩
	print end		--⑪
	resume 2: 	true	coroutine end --⑫
	
分析程序不难发现，当运行了⑥之后，程序把变量1传入function，也就是开始执行①，之后，遇到了yield函数，程序suspended，此时resume返回三个量，true、100、1，其中true表示程序运行没有问题，100是yield的参数，1是传入的参数，也就是说，resume遇到yield的时候，会返回状态的同时返回yield的所有值，之后执行第二次resume，也就是⑦，此时接着yield的部分往下运行，从⑨⑩的顺序，特别是⑩的返回内容为2，可以发现，程序是先将第二个resume的参数传入了yield函数，让yield返回传入的参数，然后继续往下执行，我们就可以得出结论，resume遇到yield就停，并将参数传入，并通过resume返回所有的参数，第二次再resume的时候就会从yield开始运行，此时yield才第一次运行，再分析⑫的结果，此时主体函数只有④部分没有运行，所以resume返回“coroutine end”

**我们总结一下，首先resume函数开始或者继续create函数的运行，每个resume都是延续上一个协同，resume会将参数传入主体函数，同时返回。如果程序没有错误，则返回true，并返回yield的所有参数，或者是主体函数的返回值。而yield函数会使程序挂起，同时也会产生返回值，返回的是额外的传入参数**

有这个基础之后，我们来看一下一个官方的例子

	--官方Demo
	 function foo (a)
       print("foo", a)
       return coroutine.yield(2*a)
     end
     
     co = coroutine.create(function (a,b)
           print("co-body", a, b)
           local r = foo(a+1)		---⑨
           print("co-body", r)
           local r, s = coroutine.yield(a+b, a-b) ----⑩
           print("co-body", r, s)
           return b, "end"
     end)
     
     print("main", coroutine.resume(co, 1, 10))  ----①
     print("main", coroutine.resume(co, "r"))    ----②
     print("main", coroutine.resume(co, "x", "y")) ---③
     print("main", coroutine.resume(co, "x", "y")) ---④
     
    下面是运行结果
    co-body	1	10
	foo	2
	main	true	4  ----⑤
	co-body	r
	main	true	11	-9 ---⑥
	co-body	x	y
	main	true	10	end  ---⑦
	main	false	cannot resume dead coroutine  ---⑧
	
怎么分析这样的程序，由入口导向，结合结果，我们先从①开始，进入coroutine.create的函数部分，然后肯定输出"co-body"这一行，接着调用foo函数，输出“foo”这一行，这时候遇到yield函数，程序挂起，resume函数返回，返回程序没有出错，所以第一个参数true，返回的应该是foo函数的结果，所以第二个参数为4，这就是我们对第一个resume的理解；

接着第二个resume，从⑨处开始（不知道为什么可以结合上面的例子进行理解），此时返回的是yield的参数，因为传入的是“r”，故此时r ＝ “r” 也就所有“co-body r”这一行，然后遇到yield函数，程序挂起，resume函数返回，程序运行没有出错，所以第一个参数为true，然后返回的是yield的所有参数，a= 1 ,b = 10,所以有返回的参数为"true 11 -9",这是对第二个resume的理解；

接着第三个resume, 从⑩开始，此时r，s分别对应着，yield函数的返回值，而返回值对应的是传入的额外的参数，也就是传入的“x”,"y"，所以有“co-body x y”这行，然后函数结束，resume函数接收的是函数的返回值,故输出"true 10 end",这是对第三个resume的理解

接着第四个resume,因为此时函数主体已经结束了。协同的状态已经为dead了，也就是说resume发生了错误，所以返回"false cannot resume dead coroutine",这就是对第四个resume的理解

###参考文档
[Lua5.3参考手册](http://cloudwu.github.io/lua53doc/manual.html#pdf-coroutine.resume)  
[Lua协同程序-轩脉刃de刀光剑影-博客](http://www.cnblogs.com/yjf512/archive/2012/05/28/2521412.html)  
[Lua协同程序-Runoob教程](http://www.runoob.com/lua/lua-coroutine.html)
	
     
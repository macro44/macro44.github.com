---
layout: post
title: "iOS App可执行文件瘦身(二)"
date: 2015-12-14 18:11:05 +0800
comments: true
categories: iOS
---
<div class="text" style="text-align:center;"><font size=6 color=red>iOS可执行文件瘦身方法</font></div>

> 在第一部分中，我们已经介绍了如何获取可执行文件组成列表，以及如何查看静态链接库的占比的方法。这一部分我们将来仔细介绍可执行文件瘦身的具体方法；

[转载于bang's blog](http://blog.cnbang.net/tech/2544/)
</br>
<!--more-->
###编译选项
1. 编译器优化级别
Build Settings->Optimization Level有几个编译优化选项，release版应该选择Fastest, Smalllest，这个选项会开启那些不增加代码大小的全部优化，并让可执行文件尽可能小。
2. 去除符号信息
Strip Linked Product / Deployment Postprocessing / Symbols Hidden by Default 在release版本应该设为yes，可以去除不必要的调试符号。Symbols Hidden by Default会把所有符号都定义成”private extern”，详细信息见[官方文档](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/MachOTopics/1-Articles/executing_files.html#//apple_ref/doc/uid/TP40001829-97021-TPXREF121)。

	这些选项目前都是XCode里release的默认选项，但旧版XCode生成的项目可能不是，可以检查一下。其他优化还可以参考官方文档—[CodeFootprint.pdf](https://developer.apple.com/legacy/library/documentation/Performance/Conceptual/CodeFootprint/CodeFootprint.pdf)
	
###第三方库统计
通过<a href="/blog/2015/12/14/ios-appke-zhi-xing-wen-jian-shou-shen-%5B%3F%5D/">(一)</a>中对可执行文件的分析可以知道第三方库的占比，从而判断是否值得去找可执行方案去替换掉这个第三方库，转载作者写了一个node.js脚本，可以通过linkmap去统计每个.o目标文件占用的体积和每个.a静态库占用的体积.具体在[详见这里](https://gist.github.com/bang590/8f3e9704f1c2661836cd)

###ARC ->MRC
具体没看懂，知道结论是可减少资源包的8%左右，有兴趣的可查看[原文](http://blog.cnbang.net/tech/2544/),但是这样程序的维护成本就上升了，如果没有特殊情况，一般不建议使用.

###无用代码
在项目里新建一个类，给它添加几个方法，但不要在任何地方import它，build完项目后观察linkmap，你会发现这个类还是被编译进可执行文件了。
按C++的经验，没有被使用到的类和方法编译器都会优化掉，不会编进最终的可执行文件，但object-c不一样，因为object-c的动态特性，它可以通过类和方法名反射获得这个类和方法进行调用，所以就算在代码里某个类没被使用到，编译器也没法保证这个类不会在运行时通过反射去调用，所以只要是在项目里的文件，无论是否又被使用到都会被编译进可执行文件。
对此我们可以通过脚本，遍历整个项目的文件，找出所有没有被引用的类文件和没有被调用的方法，在保证没有其他地方动态调用的情况下把它们去掉。如果整个项目历时很长，历时代码遗留较多，这个清理对可执行文件省出的空间还是挺可观的。

###类/方法名的长度
观察linkmap可以发现每个类和方法名都在__cstring段里都存了相应的字符串值，所以类和方法名的长短也是对可执行文件大小是有影响的，原因还是object-c的动态特性，因为需要通过类/方法名反射找到这个类/方法进行调用，object-c对象模型会把类/方法名字符串都保存下来。
对此我们可以考虑在编译前把所有类和方法名进行混淆，跟压缩js一样，把长名字替换成短名字，这样做的好处除了缩小体积外，还对安全性有很大提升，别人拿到可执行文件对它class-dump出来的结果都是混淆后的类和方法名，就无法从类和方法名中猜出某个方法是做什么的，就难以挂钩子进行hack。不过这样做有个缺点，就是crash堆栈反解出来的堆栈方法名会是混淆后的，需要再加一层混淆->原名的转换，实现和使用成本有点高。
实际上这部分占用的长度比较小，中型项目也就几百K，对安全性要求高的情况可以试试。

###冗余字符串
代码上定义的所有静态字符串都会记录在在可执行文件的__cstring段，如果项目里Log非常多，这个空间占用也是可观的，也有几百K的大小，可以考虑清理所有冗余的字符串。另外如果有特别长的字符串，建议抽离保存成静态文件，因为AppStore对可执行文件加密导致压缩率低，特别长的字符串抽离成静态资源文件后压缩率会比在可执行文件里高很多。


###列一个清单
![效果展示](http://7xopon.com1.z0.glb.clouddn.com/2015121403.png)
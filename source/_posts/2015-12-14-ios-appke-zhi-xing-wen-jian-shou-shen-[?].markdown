---
layout: post
title: "iOS App可执行文件瘦身(一)"
date: 2015-12-14 16:17:28 +0800
comments: true
categories: iOS
---
<div class="text" style="text-align:center;"><font size=6 color=red>iOS App可执行文件的组成</font></div>

> 面试的时候遇到的面试题，如何给App瘦身，之前有看过类似的题目，讲的大概就是两个方面，一、给资源文件瘦身，2、能下载处理的尽量通过后续下载来处理。但没有实际操作过。所以总感觉回答的不是那么个味道。回来问同事以及查资料后果然，需要两个方面来瘦身：1、减少资源包的大小；2、减少可执行文件的大小。1在本博客后续博客中会提到如何瘦身，本文主要讲解可执行文件瘦身的一部分，App可执行文件的组成；

<!--more-->

[转载于bang's blog ](http://blog.cnbang.net/tech/2296/)

###查看App可执行文件组成的方法
1. XCode开启编译选项Write Link Map File
Xcode -> Project -> Build Setting ->搜索map ->设置Write Link Map File为Yes  
![展示图片](http://7xopon.com1.z0.glb.clouddn.com/2015121401.png)

2. 编译后，通过路径查找找到图中txt文件对应的文件
   具体路径: ~/Library/Developer/Xcode/DerivedData/XXX-xxxxxxxxxxxx/Build/Intermediates/XXX.build/Debug-iphoneos/XXX.build/XXX-LinkMap-xxx.txt  
   （这个LinkMap展示了整个App中可执行文件的全貌，列出了编译后的每一个.o文件，包括.a文件里的，以及每一个目标文件的代码段，数据段存储详情等）
   
3. 以我目前手上的项目为例，在linkMap中的文件列表，有部分删减，为了方便讲解
![展示图片](http://7xopon.com1.z0.glb.clouddn.com/2015121402.png)

	第一部分(Object files部分)含义：  
	前面中括号表示文件的编号，在后面的部分中会用到,后面表示可执行文件的位置
	
	第二部分(Sections部分)含义:  
	Address表示偏移位置,Size表示大小,Segment表示段类型(`__TEXT`,保存程序代码段编译后的机器码，`__Data`),Section表示段名称(`__text`表示编译后的程序可执行语句，`__data`表示已初始化的全局变量和局部静态变量，`__bss`表示未初始化的全局变量和局部静态变量,`__csstring`表示代码里的字符串常量)
	
	可以发现每一行的数据都是紧跟在上一行数据后面， 即第二行到Address为第一行的Address加上第二行的Size组成
	
	第三部分(symbols部分)含义: 
	Address 表示偏移位置,第二部分表示占用大小,第三部分表示所属文件序号，对应上述Object files列表，最后是文件名称
	
4. 使用  
	在了解了可执行文件的组成后，我们可以通过计算来获取某个静态库在项目中的占比，通过将.a静态库的.o加起来，就是这个静态库可执行部分占整个app空间的大小。（但是需要注意，此大小并不等于静态库的物理大小，此大小只是可执行文件的大小，要小于静态库的物理大小）

	
	
	
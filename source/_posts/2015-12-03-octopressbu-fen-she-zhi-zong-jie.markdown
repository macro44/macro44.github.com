---
layout: post
title: "Octopress部分设置总结"
date: 2015-12-03 17:12:12 +0800
comments: true
categories: 其他
---
 > 此文主要用来总结在设置Octopress时遇到的一些需求，但不好找的部分
 
<!--more-->
</br> 
###在首页只显示部分文章内容

**修改原因:**默认情况下，首页会显示每一篇文章的全部，这样给用户的感觉不是很好，首页应该只显示一个对文章的简单介绍，这样既方便了用户查看总体的文件介绍，找到感兴趣的，又不会给用户首页和文章详情是一样的感觉，引导用户点击进入查看全文

**设置原理:**在_config.yml里面设置了分隔符<font color=red size=3>excerpt_sepatator</font>

**设置方法:**将_config.yml里面的<font color=red size=3>excerpt_link</font>字段对应的值中*”Continue reading“*部分引导用户点击查看全文的文字,并在相应的文章对应的Markdown文档中,找到恰当的地方，加入代码`<!--more-->`

**设置以及效果展示**    
![展示1](http://7xopon.com1.z0.glb.clouddn.com/2015120302.png)
![展示2](http://7xopon.com1.z0.glb.clouddn.com/2015120301.png)

</br>
###设置字体、背景色

**修改原因:**可能在撰写markdown文章的时候需要用到文本强调，但markdown常用的语法中并没有提供语法用于改变字体以及背景色 

**设置原理:**markdown的兼容HMTML，可以用部分HTML来实现我们想要的效果

**设置方法：**   

1. <font color=red size=2>设置字体</font> 在需要改变属性的文本加上标签`<font color=字体颜色 size=字体大小>文本</font>`  
2. <font color=red size=2>设置背景色</font> 在需要改变背景的文本加上标签`<td bgcolor=背景颜色>文本</td>`

**设置Demo**  

	<font color=red size=5>Markdown</font>

&nbsp;&nbsp;&nbsp;&nbsp;<font color=red size=5>Markdown</font>

	<table><tr><td bgcolor=#ff4500>Markdown</td></tr></table>
	或<span style=background-color:#ff4500>Markdown</span>	
<table><tr><td bgcolor=#ff4500><font color=white size=2>Markdown</font></td></tr></table>
&nbsp;&nbsp;&nbsp;&nbsp;<span style=background-color:#ff4500><font color=white size=2>Markdown</font></span>

</br>
###设置空格
**修改原因:**默认的情况下markdown语法会顶格写，这样不符合一些写作习惯，且在markdown文档中无论敲多少空格，默认只显示一个文本间距，对其实现成难题

**设置原理:**markdown的兼容HMTML，可以用部分HTML来实现我们想要的效果

**设置方法：** 通过`&nbsp;`来实现字符空格(注意每一个&nbsp相当于一个字符,但中文为两个字符)

**设置效果**  
<font color=red size=4>Mark&nbsp;&nbsp;Down</font>  
<font color=red size=4>你&nbsp;&nbsp;&nbsp;&nbsp;好</font>
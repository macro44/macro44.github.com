---
layout: post
title: "HTML基础标签总结"
date: 2015-12-16 10:31:14 +0800
comments: true
categories: HTML
---
> 离不开H5，就尽情的享受HTML带来的方便。学H5，从基本的HTML开始；

###目录:
<a href=#1>1. HTML基本文档</a><br/>
<a href=#2>2. HTML基本标签</a><br/>
<a href=#3>3. HTML文本格式化</a><br/>
<a href=#4>4. 链接</a><br/>
<a href=#5>5. 样式/区块</a><br/>
<a href=#6>6. 表格</a><br/>
<a href=#7>7. 列表</a><br/>
<a href=#8>8. 框架</a><br/>
<a href=#9>9. 表单</a><br/>
<a href=#10>10. 实体</a><br/>

<!--more-->

<a id=1>1. HTML基本文档</a>
	
	<!DOCTYPE html>
	<html>
	<head>
	<title>文档标题</title>
	</head>

	<body>
	文本...
	</body>
	</html>
	
<a id=2>2. HTML基本标签</a><br/>

	<h1>这是一级标题</h1>
	<h2>这是二级标题</h2>
	<h3>这是三级标题</h3>
	...
	<h6>这是最小的标题</h6>
	<p>这是一个段落</p>
	<br/>  换行
	<hr/>  水平线
	<!--这是注释-->
	
<a id=3>3. HTML文本格式化</a><br/>
	
	<b>粗体文本</b>
	<i>斜体文本</i>
	<strong>加重语气－粗体显示</strong>
	<em>着重语气-斜体显示</em>
	<small>更小的文本</small>
	<sub>定义下标字</sub>
	<sup>定义上标字</sup>
	<ins>定义删除字</ins>
	<del>定义下划线<del>
	
	<code>计算机代码</code>
	<kbd>键盘输入</kbd>
	<samp>计算机代码样本</samp>
	<var>定义变量</var>
	<pre>预格式文本</pre>
	
	<abbr>定义缩写</abbr>
	<address>定义地址</address>
	<bdo>定义文本的方向</bdo>
	<q>短引用</q>
	<blockquote>从另一个源引用的部分</blockquote>
	<cite>一般与blockquote连用，用来表示引用的源</cite>
	
<a id=4>4. 链接</a><br/>

	普通的链接: <a href="url">链接文本</a>
	图片: <img src="url" alt="替换文本" height="高" width="宽">
	图片链接: <a href="url"><img src="url" alt="替换文本"></a>
	邮件链接:	 <a href="mailto:mail_xxx@example.com">发送Email</a>
	书签: <a id="tips">提示部分</a>
		<a href="#tips">跳转到提示部分</a>

<a id=5>5. 样式/区块</a><br/>

	样式写法(一):
	<style type="text/css">
	body {background-color:yellow;}
	h1 {color:red;text-align:center;}
	p  {color:green;}
	</style>	
	
	样式写法(二):
	<body style="background-color:red;text-align:center;">...</body>
	
	样式写法(三):
	<link rel="当前文档与被链接文档之间的关系" type="text/css" href="url">
	
	区块:主要功能是将页面模块分化，每一个模块有每一个模块的样式，方便进行处理
	<div>块级元素</div>
	
	<span>内联元素</span>
	
<a id=6>6. 表格</a><br/>

	<table border="边宽"></table>
	<caption>表的标题</caption>
	<tr>定义一行</tr>
	<th>表头</th>
	<td>元素</td>
	colspan表示可跨越的列数
	rowspan表示可跨越的行数
	cellspacing 表示表格与表格边缘的距离
	
	例子：
	<table border="1" style="text-align:center" cellspacing="0">
	<caption>表格Demo</caption>
	<tr>
		<th>A</th>
		<th>B</th>
	</tr>
	<tr>
		<td colspan="2">a</td>
	</tr>
	</table>

<table border="1" style="text-align:center" cellspacing="0">
<caption>表格Demo</caption>
<tr>
	<th>A</th>
	<th>B</th>
</tr>
<tr>
	<td colspan="2">a</td>
</tr>
</table>

<a id=7>7. 列表</a><br/>
	
	<ol>定义有序列表</ol>
	<ul>定义无序列表</ul>
	<dl>定义自定义列表</dl>
	<dt>自定义列表项目</dt>
	<dd>自定义列表的描述</dd>
	type 表示列表的类型
	
	有序列表
	<ol type="1" start="50">
		<li>第一项</li>
		<li>第二项</li>
		<li>第三项</li>
	</ol>
	
	无序列表
	<ul>
		<li>第一项</li>
		<li>第二项</li>
	</ul>
	
	自定义列表 
	<dl>
		<dt>项目名1</dt>
			<dd>项目名描述1</dd>
		<dt>项目名2</dt>
			<dd>项目描述2</dd>
	</dl>
	
<ol type="a" start="3">
		<li>第一项</li>
		<li>第二项</li>
		<li>第三项</li>
	</ol>
	
<a id=8>8. 框架</a><br/>
	
	<iframe src="url" width="宽" height="高" name="名称">定义框架</iframe>
	
<a id=9>9. 表单</a><br/>

	<form>表单元素</form>
	<input type="类型">输入</input>
	<textarea>文本框</textarea>
	<label>为 input 元素定义标注</label>
	<fieldset>在相关表单元素周围绘制边框/fieldset>
	<legend>为<fieldset>元素定义标题</legend>
	<select>下拉选项列表</select>
	<optgroup>定义选项组</optgroup>
	<option><select>的选项</option>
	<button>定义一个按钮</button>
	<datalist>定义一个<input>元素可能选项列表</datalist>
	<output>定义一个计算结果</output>
	
	<form>
	<fieldset>
	<legend>表单DEMO</legend>
	输入框: <input type="text" name="user"><br/>
	密码框: <input type="password" name="pwd"><br/>
	单选框: <input type="radio" name="singleChoose" value="1"><br/>
	复选框: <input type="checkbox" name="muilChoose" value="1"><br/>
	提交按钮: <input type="submit" value="提交"><br/>
	下拉列表: <select>
		<optgroup label="分类1">
				<option value="1">1</option>
		</optgroup>
		<optgroup label="分类2" disabled>
				<option value="2" selected>2</option>		</optgroup>
		</select><br/>
	文本框: <textarea rows="2" cols="30">我是一个文本框</textarea>
	按钮: <input type="button" value="按钮">或者<button type="button">click</button>
	</fieldset>
	</form>
<fieldset>
	<legend>表单DEMO</legend>
输入框: <input type="text" name="user"><br/>
密码框: <input type="password" name="pwd"><br/>
单选框: <input type="radio" name="singleChoose" value="1"><br/>
复选框: <input type="checkbox" name="muilChoose" value="1"><br/>
提交按钮: <input type="submit" value="提交"><br/>
下拉列表: <select>
		<optgroup label="分类1">
				<option value="1">1</option>
		</optgroup>
		<optgroup label="分类2" disabled>
				<option value="2" selected>2</option>		</optgroup>
		</select><br/>
文本框: <textarea rows="2" cols="30">我是一个文本框</textarea><br/>
按钮: <input type="button" value="按钮"><br/>
<button type="button">click</button>
</fieldset>

<a id=10>10. 实体</a><br/>

	< &lt; &#60;
	> $gt; &#62;
	© &#169;

<a href="http://www.runoob.com/html/html-tutorial.html" style="text-decoration:none;" target="_blank">Runoob教程链接</a>
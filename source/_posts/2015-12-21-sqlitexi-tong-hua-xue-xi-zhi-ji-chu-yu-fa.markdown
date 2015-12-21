---
layout: post
title: "sqlite系统化学习之基础语法"
date: 2015-12-21 15:07:31 +0800
comments: true
categories: SQLite
---
> 之前在项目中使用sqlite的时候，都是使用现成的基本框架，没有具体细致的去了解sqlite的从无到有的使用，最近有时间看一下sqlite语法，分为两个部分来总结一下sqlite的语法，第一部分主要用来写一些sqlite语法。主要分为两篇，这是之一，基础语法篇，还有一篇高级语法篇。第二部分用来分析在iOS项目中sqlite框架的构成，以及基本使用。

本教程目录：
<script>
function alertWarming(){
	alert("adsfas");
}
</script>

<dl>
	<dt><a href=#1.1>1. SQLite数据类型</a></dt>
		<dd><a href=#1.1>1.1 SQLite存储类</a><br/>    
			<a href=#1.2>1.2 Boolean数据类型</a><br/>
		    <a href=#1.3>1.3 Date与Time数据类型</a><br/>
		    <a href=#1.4>1.4 SQLite 亲和(Affinity)类型</a>
		</dd>
	<dt><a href=#2.1 onclick="alertWarming()">2. SQLite数据库</a><br/></dt>
		<dd><a href=#2.1 onclick="alertWarming()">2.1 SQLite创建数据库</a><br/>
			<a href=#2.2 onclick="alertWarming()">2.2 SQLite关联数据库</a><br/>
			<a href=#2.3 onclick="alertWarming()">2.3 SQLite分离数据库</a>
		</dd>
</dl>

<a id=1.1>1.1 SQLite存储类</a>

| 存储类 | 描述		|
|-------|----------	|
| NULL	|值是一个NULL值.|
| INTEGER | 值是一个带符号的整形，根据值的大小存储在1、2、3、4、6、8字节中|
|REAL| 值是一个浮点值,存储为8字节点的IEE浮点数字|
|TEXT|值是一个文本字符串|
|BLOB|用于存储blob数据段，如图片、语音、视频等|

<a id=1.2>1.2 Boolean数据类型</a><br/>
	Sqlite没有单独的Boolean存储类,布尔值被存储为整数0和1

<a id=1.3>1.3 Date与Time数据类型</a><br/>
SQLite没有单独的用语存储日期或时间的存储类，但是SQLite能够把日期和时间存储为TEXT、REAL或INTEGER值.

|存储类|日期格式|
|-----|-------|
|TEXT|格式为"YYYY-MM-DD HH:MM:SS.SSS"的日期|
|REAL|从公元前4714年11月24日格林尼治的正午开始算起的天数|
|INTEGER|从1970-01－01 00:00:00 UTC算起的秒数.|

<a id=1.4>1.4 SQLite 亲和(Affinity)类型</a>

<table border='1' cellspacing="0" style="text-align:center">
<tr>
	<th>Affinity</th>
	<th>描述</th>
</tr>
<tr>
	<td>TEXT</td>
	<td>该列使用存储类NULL、TEXT或BLOB存储所有数据</td>
</tr>
<tr>
	<td>NUMERIC</td>
	<td>该列可以包含使用所有五个存储类的值。</td>
</tr>
<tr>
	<td>INTEGER</td>
	<td>与带有 NUMERIC affinity 的列相同，在 CAST 表达式中带有异常。</td>
</tr>
<tr>
	<td>REAL</td>
	<td>与带有 NUMERIC affinity 的列相似，不同的是，它会强制把整数值转换为浮点表示。</td>
</tr>
<tr>
	<td>NONE</td>
	<td>	带有 affinity NONE 的列，不会优先使用哪个存储类，也不会尝试把数据从一个存储类强制转换为另一个存储类。</td>
</tr>
</table>

Affinity类型每一种包含的具体类型参照表:
<div align="center">
<table border='1' cellspacing="0" style="text-align:center">
<tr>
	<th>数据类型</th>
	<th>Affinity</th>
<tr>
<tr>
	<td>
		<ul>
			<li>INT</li>
			<li>INTEGER</li>
			<li>TINYINT</li>
			<li>SMALLINT</li>
			<li>MEDIUMINT</li>
			<li>BIGINT</li>
			<li>UNSIGNED BIG INT</li>
			<li>INT2</li>
			<li>INT8</li>
		</ul>
	</td>
	<td>INTEGER</td>
</tr>
<tr>
	<td>
		<ul>
			<li>CHARACTER(20)</li>
			<li>VARCHAR(255)</li>
			<li>VARYING CHARACTER(255)</li>
			<li>NCHAR(55)</li>
			<li>NATIVE CHARACTER(70)</li>
			<li>NVARCHAR(100)</li>
			<li>TEXT</li>
			<li>CLOB</li>
		 </ul>
	</td>
	<td>TEXT</td>
</tr>
<tr>
	<td>
		<ul>
			<li>BLOB</li>
			<li>no datatype specified</li>
		</ul>
	</td>
	<td>NONE</td>
</tr>
<tr>
	<td>
		<ul>
			<li>REAL</li>
			<li>DOUBLE</li>
			<li>DOUBLE PRECISION</li>
			<li>FLOAT</li>
		</ul>
	</td>
	<td>REAL</td>
</tr>
<tr>
	<td>
		<ul>
			<li>NUMERIC</li>
			<li>DECIMAL(10,5)</li>
			<li>BOOLEAN</li>
			<li>DATE</li>
			<li>DATETIME</li>
		</ul>
	</td>
	<td>NUMERIC</td>
</tr>
</table>
</div>

<br/>
<a id=2.1>2.1 SQLite创建数据库</a><br/>


<a id=2.2>2.2 SQLite关联数据库</a><br/>

<a id=2.3>2.3 SQLite分离数据库</a><br/>
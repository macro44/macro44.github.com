---
layout: post
title: "sqlite系统化学习之基础语法"
date: 2015-12-21 15:07:31 +0800
comments: true
categories: SQLite
---
> 之前在项目中使用sqlite的时候，都是使用现成的基本框架，没有具体细致的去了解sqlite的从无到有的使用，最近有时间看一下sqlite语法，分为两个部分来总结一下sqlite的语法，第一部分主要用来写一些sqlite语法。主要分为两篇，这是之一，基础语法篇，还有一篇高级语法篇。第二部分用来分析在iOS项目中sqlite框架的构成，以及基本使用。

<!--more-->

本教程目录：
<script>
function alertWarming(){
	alert("因为本部分在iOS上用不上，所以忽略！");
}
function backToMenu(){
	window.scrollTo(0,100);
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
		
	<dt><a href=#3.1>3. SQLite 表</a><br/></dt>
		<dd><a href=#3.1>3.1 SQLite创建表</a></br>
			<a href=#3.2>3.2 SQLite删除表</a>
		</dd>
		
	<dt><a href=#4.1>4. SQLite 运算符</a></dt>
		<dd><a href=#4.1>4.1 算数运算符</a><br/>
			<a href=#4.2>4.2 比较运算符</a><br/>
			<a href=#4.3>4.3 逻辑运算符</a><br/>
			<a href=#4.4>4.4 位运算符</a>
		</dd>
	
	</dt>	
		
	<dt><a href=#6.1>6. SQL基本操作</a><br/></dt>
		<dd><a href=#6.1>6.1 SQL Insert语句</a><br/>
			<a href=#6.2>6.2 SQL Delete语句</a><br/>
			<a href=#6.3>6.3 SQL Update语句</a><br/>
			<a href=#6.4>6.4 SQL Select语句</a>
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
**因为本部分在iOS上用不上，所以忽略！**
<input type="button" value="返回目录" onclick=backToMenu()>

<a id=2.2>2.2 SQLite关联数据库</a><br/>
**因为本部分在iOS上用不上，所以忽略！**
<input type="button" value="返回目录" onclick=backToMenu()>

<a id=2.3>2.3 SQLite分离数据库</a><br/>
**因为本部分在iOS上用不上，所以忽略！**
<input type="button" value="返回目录" onclick=backToMenu()>

<a id=3.1>3.1 SQLite创建表</a><br>
	
	基本语法结构: CREATE TABLE table_name(
					column1  datatype,
					...
				);
<a id=3.2>3.2 SQLite删除表</a><br/>

	基本语法结构: DROP TABLE table_name;
<font color=red size=3>\*\*使用此命令需注意，一旦表删除，表中的信息也将永久删除\*\*</font>

<a id=4.1>4.1 算数运算符</a><br/>
<table border="1" cellspacing="0">
<tr>
	<th style="width:20%">运算符
	</th>
	<th style="width:45%">描述
	</th>
	<th>实例
	</th>
</tr>
<tr>
	<td>+</td>
	<td>加法 - 把运算符两边的值相加</td>
	<td> a + b 将得到 30</td>
</tr>
<tr>
	<td>-</td><td>减法 - 左操作数减去右操作数</td>
	<td> a - b 将得到 -10</td>
</tr>
<tr>
	<td>*</td>
	<td>乘法 - 把运算符两边的值相乘</td>
	<td> a * b 将得到 200</td>
</tr>
<tr>
	<td>/</td>
	<td>除法 - 左操作数除以右操作数</td>
	<td> b / a 将得到 2</td>
</tr>
<tr>
	<td>%</td>
	<td>取模 - 左操作数除以右操作数后得到的余数</td>
	<td> b % a将得到 0</td>
</tr>
</table>

<a id=4.2>4.2 比较运算符</a><br/>

<table border=1 cellspacing=0>
<tbody>
<tr>
	<th style="width:20%">运算符</th>
	<th style="width:45%">描述</th>
	<th>实例</th>
</tr>
<tr>
	<td>==</td>
	<td>检查两个操作数的值是否相等，如果相等则条件为真。</td>
	<td> (a == b) 不为真。</td>
</tr>
<tr>
	<td>=</td>
	<td>检查两个操作数的值是否相等，如果相等则条件为真。</td>
	<td> (a = b) 不为真。</td>
</tr>
<tr>
	<td>!=</td>
	<td>检查两个操作数的值是否相等，如果不相等则条件为真。</td>
	<td> (a != b) 为真。</td>
</tr>
<tr>
	<td>&lt;&gt;</td>
	<td>检查两个操作数的值是否相等，如果不相等则条件为真。</td>
	<td> (a &lt;&gt; b) 为真。</td>
</tr>
<tr>
	<td>&gt;</td>
	<td>检查左操作数的值是否大于右操作数的值，如果是则条件为真。</td>
	<td> (a &gt; b) 不为真。</td>
</tr>
<tr>
	<td>&lt;</td>
	<td>检查左操作数的值是否小于右操作数的值，如果是则条件为真。</td>
	<td> (a &lt; b) 为真。</td>
</tr>
<tr>
	<td>&gt;=</td>
	<td>检查左操作数的值是否大于等于右操作数的值，如果是则条件为真。</td>
	<td> (a &gt;= b) 不为真。</td>
</tr>
<tr>
	<td>&lt;=</td>
	<td>检查左操作数的值是否小于等于右操作数的值，如果是则条件为真。</td>
	<td> (a &lt;= b) 为真。</td>
</tr>
<tr>
	<td>!&lt;</td>
	<td>检查左操作数的值是否不小于右操作数的值，如果是则条件为真。</td>
	<td> (a !&lt; b) 为假。</td>
</tr>
<tr>
	<td>!&gt;</td>
	<td>检查左操作数的值是否不大于右操作数的值，如果是则条件为真。</td>
	<td> (a !&gt; b) 为真。</td>
</tr>
</table>
<a id=4.3>4.3 逻辑运算符</a><br/>

<table border=1 cellspacing=0>
<tr>
	<th style="width:20%">运算符</th>
	<th style="width:80%">描述</th>
</tr>
<tr>
	<td>AND</td>
	<td>AND 运算符允许在一个 SQL 语句的 WHERE 子句中的多个条件的存在。</td>
</tr>
<tr>
	<td>BETWEEN</td>
	<td>BETWEEN 运算符用于在给定最小值和最大值范围内的一系列值中搜索值。</td>
</tr>
<tr>
	<td>EXISTS</td>
	<td>EXISTS 运算符用于在满足一定条件的指定表中搜索行的存在。</td>
</tr>
<tr>
	<td>IN</td>
	<td>IN 运算符用于把某个值与一系列指定列表的值进行比较。</td>
</tr>
<tr>
	<td>NOT IN</td>
	<td>IN 运算符的对立面，用于把某个值与不在一系列指定列表的值进行比较。</td>
</tr>
<tr>
	<td>LIKE</td>
	<td>LIKE 运算符用于把某个值与使用通配符运算符的相似值进行比较。</td>
</tr>
<tr>
	<td>GLOB</td>
	<td>GLOB 运算符用于把某个值与使用通配符运算符的相似值进行比较。GLOB 与 LIKE 不同之处在于，它是大小写敏感的。</td>
</tr>
<tr>
	<td>NOT</td>
	<td>NOT 运算符是所用的逻辑运算符的对立面。比如 NOT EXISTS、NOT BETWEEN、NOT IN，等等。<b>它是否定运算符。</b></td>
</tr>
<tr>
	<td>OR</td>
	<td>OR 运算符用于结合一个 SQL 语句的 WHERE 子句中的多个条件。</td>
</tr>
<tr>
	<td>IS NULL</td>
	<td>NULL 运算符用于把某个值与 NULL 值进行比较。</td>
</tr>
<tr>
	<td>IS</td>
	<td>IS 运算符与 = 相似。</td>
</tr>
<tr>
	<td>IS NOT</td>
	<td>IS NOT 运算符与 != 相似。</td>
</tr>
<tr>
	<td>||</td>
	<td>连接两个不同的字符串，得到一个新的字符串。</td>
</tr>
<tr>
	<td>UNIQUE</td>
	<td>UNIQUE 运算符搜索指定表中的每一行，确保唯一性（无重复）。</td>
</tr>
</table>

<a id=4.4>4.4 位运算符</a><br/>

<table border=1 cellspacing=0>
<tr>
	<th width="20%">运算符</th>
	<th width="45%">描述</th>
	<th>实例</th>
</tr>
<tr>
	<td>&amp;</td>
	<td>如果同时存在于两个操作数中，二进制 AND 运算符复制一位到结果中。</td>
	<td> (A &amp; B) 将得到 12，即为 0000 1100</td>
</tr>
<tr>
	<td>|</td>
	<td>如果存在于任一操作数中，二进制 OR 运算符复制一位到结果中。</td>
	<td> (A | B) 将得到 61，即为 0011 1101</td>
</tr>
<tr>
	<td>~</td>
	<td>二进制补码运算符是一元运算符，具有"翻转"位效应。</td>
	<td> (~A ) 将得到 -61，即为 1100 0011，2 的补码形式，带符号的二进制数。</td>
</tr>
<tr>
	<td>&lt;&lt;</td>
	<td>二进制左移运算符。左操作数的值向左移动右操作数指定的位数。</td>
	<td> A &lt;&lt; 2 将得到 240，即为 1111 0000</td>
</tr>
<tr>
	<td>&gt;&gt;</td>
	<td> 二进制右移运算符。左操作数的值向右移动右操作数指定的位数。</td>
	<td> A &gt;&gt; 2 将得到 15，即为 0000 1111</td>
</tr>
</table>

<a id=6.1>6.1 SQL Insert语句</a><br/>

	基本语法:INSERT INTO TABLE_NAME (column1,...columnN) VALUES (value1,...valueN);
	当要为表中所有列添加值，这时可以不用指定列名称,此时语法结构可以为:
	INSERT INTO TABLE_NAME VALUES (value1, ...valueN);
<a id=6.2>6.2 SQL Delete语句</a><br/>
	
	基本语法结构:DELETE FROM table_name WHERE [condition];		
<a id=6.3>6.3 SQL Update语句</a><br/>

	基本语法结构:UPDATE table_name SET column1 = value1...., columnN = valueN 
	WHERE [condition];

<a id=6.4>6.4 SQL Select语句</a>

	基本语法结构:SELECT column1, column2,... columnN FROM table_name;
	如果要查询所有的数据，可以用 select * from table_name;
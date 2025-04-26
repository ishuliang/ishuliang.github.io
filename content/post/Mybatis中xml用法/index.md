---
title: MyBatis的XML中元素格式
date: 2024-11-07 10:46:34
description: "mybatis的xml文件中有许多大于小于各种符号，需要使用OGNL标签"
image: index.jpg
math: false
hidden: false
comments: false
draft: false
---


## 背景

  在使用MyBatis根据时间范围**动态SQL拼接**查询数据库时报错：Caused by: Caused by: org.xml.sax.SAXParseException: The content of elements must consist of well-formed character data or markup.（元素内容必须由格式正确的字符数据或标记组成。）

## 解决办法

MyBatis 在编写动态SQL的时候，是基于OGNL的。

1. 不改变符号，依然使用 “ > ” 或者 “ < ” ，则需要使用 OGNL 标签，将该符号包起来，可以是包裹单个符号，也可以将整句包裹寄来，示例SQL如下：

```sql
<select id="getLoginLogByPage" resultType="com.mcp.entity.LoginLog">
	SELECT l.id, l.job_number, l.name, l.createDate, l.ip_addr
		, l.remark
	FROM loginlog l
	<where>
		<if test="jobNumber != null" >
			<![CDATA[ and l.job_number > #{jobNumber}]]>
		</if>
	</where>
	GROUP BY l.today_date, l.ip_address;
 
</select>
```

2. 使用 OGNL 语法表达式，在 OGNL 语法表达式中不能直接写符号，需要要使用其转义后的字符，对应如下：

| 符号  | 转义字符 |   说明   |
| ----- | -------- | :------: |
| <     | \&lt;    |   小于   |
| <=    | \&lt;=   | 小于等于 |
| >     | \&gt;    |   大于   |
| >=    | \&gt;=   | 大于等于 |
| &     | \&amp;   |  逻辑与  |
| 'xxx' | \&apos;  |  单引号  |
| "xxx" | \&quot;  |  双引号  |

```sql
<select id="getLoginLogByPage" resultType="com.mcp.entity.LoginLog">
	SELECT l.id, l.job_number, l.name, l.createDate, l.ip_addr
		, l.remark
	FROM loginlog l
	<where>
		<if test="jobNumber != null" >
			and l.job_number &gt; #{jobNumber}
		</if>
	</where>
	GROUP BY l.today_date, l.ip_address;
 
</select>
```

参考的这篇文章：https://blog.csdn.net/Hello_World_QWP/article/details/86513179

---
title: MySQL和Oracdatetime
date: 2025-02-27 09:59:16
description: "工作中遇到切换数据库，碰到了时间切换"
image: 20250227.jpg
slug: /20250227
categories: "work"
---

## 如何把一个时间字符串转为数据库中datetime类型

MySQL中不存在`TO_DATE(str,format)`方法，但是可以使用`STR_TO_DATE(str,format)`方法：

```sql
STR_TO_DATE ( '2025-02-27 06:59:59', '%Y-%m-%d %H:%i:%s' )
```

Oracle中就可以直接用`TO_DATE(str,format)`方法，但是`format`需要注意一下，是不一样的：

```sql
TO_DATE( '2025-02-27 06:59:59', 'yyyy-MM-dd hh24:mi:ss' )
```

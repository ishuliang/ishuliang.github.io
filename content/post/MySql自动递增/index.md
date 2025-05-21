---
title: "Mysql自动递增"
description: mysql自动递增传0一会触发自动递增
date: 2025-05-19T14:15:19+08:00
image: 20250519.JPG
slug: 20250519
---

# mysql自动自增

## 起因

今天上午，朋友给我发了一个sql语句，然我帮看一下为什么报错，我看到了报错信息：

![报错信息](./1.png)

第一反应就是主键重复了，但是结果并不是，知道我看了建表语句，发现id是`auto_increment`，我想可能与自增有关，所以我自己建了表查看了一下

```sql
CREATE TABLE `c_test` (
  `id` tinyint(4) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4;
```

然后手动插入了几条数据

```sql
INSERT INTO `study`.`c_test` (`id`, `name`) VALUES (0, '0');
INSERT INTO `study`.`c_test` (`id`, `name`) VALUES (1, '1');
```

我发现也是报相同的问题，因为不是提交的事务，所以第一句插入成功了，但是id确变成了1，自动递增确实是从1开始的，但是现在我指定了id为0，为什么变1了呢？

## 查询资料并解决

我查了网上的信息，发现说是因为mysql自动递增的时候会根据null或者0来判断是否自增，然后我又去看了一下mysql的文档：https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_no_auto_value_on_zero

![mysql文档](./2.png)

这句话就是说当插入是0或者null时会自动生成序号，然后我查了一下当前库的`sql_mode`：

```sql
SELECT @@sql_mode
```

![sql_mode](3.png)

没有改`sql_mode`值，那确实插入0会变1，导致主键重复。
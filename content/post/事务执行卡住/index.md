---
description: "在工作中遇到了一个接口长时间没反应，最后定位到sql语句执行卡住"
title: "MySQL事务执行卡住"
date: 2025-03-06 17:38:48
image: 20250306.jpg
slug: 20250306
categories: "work"
---
## 问题

我今天执行一个方法，该方法有`@Transactional(rollbackFor = Exception.class)`,方法执行没有报错，在返回之后提交事务，会执行到`private native int socketRead0(FileDescriptor fd, byte b[], int off, int len, int timeout)`阻塞起来，程序无法进行，接口报错超时。

## 解决办法：

我重启了MySQL服务，就正常了，治标不治本。

## 猜测：

到提交这里阻塞，而且重启MySQL就恢复正常了，应该是MySQL服务中有事务阻塞了。

下次遇见应该查一下MySQL的阻塞事务，具体看一下是什么原因，这次没找到原因所以没机会复现了，记录一下，便于下次排查纠错。

## 最终结果
数据库没存储空间了
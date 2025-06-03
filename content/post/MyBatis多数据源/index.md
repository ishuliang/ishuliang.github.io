---
title: mybatis多数据源问题
date: 2025-02-11 11:27:26
description: "mybatis多数据源是怎么实现的，为什么有些方法可以实现，有些不行"
image: 20250211.jpg
slug: /20250211
categories: "work"
---

# mybatis多数据源问题

## 配置

参考网站（https://baomidou.com/guides/dynamic-datasource/）

还有原理解析的一个参考网站（https://www.cnblogs.com/fnlingnzb-learner/p/16805784.html）

## 问题

我在远程调用时，直接引入service层（再mapper层上写了@DS），调用saveOrUpdateBatch，自带的方法，多数据源没有生效，我在这个远程调用的方法上加@DS，就生效了。

## 猜测

@DS用的切面，我直接使用serviceImpl类自带的实现类中方法，这些方法走的SqlSession，并没有走到mybatis的mapper层，所以并没有切面调用切换数据源，还是走的默认数据源。

## TODO 空闲时间，研究一下这个问题

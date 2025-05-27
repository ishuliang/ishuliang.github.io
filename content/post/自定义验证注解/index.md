---
title: "自定义验证入参方法"
description: 使用@AssertTrue(message = "")定义一个方法实现互斥验证参数
date: 2025-05-27T17:46:31+08:00
image: 20250527.jpg
slug: 20250527
---

# 互斥验证参数

## 背景

在开发一个查询方式时，需要根据日期查询或者根据开始和结束时间进行范围查询，我一开始的想法时使用 if 进行判断，之后问了 ai，给我提供了一个新思路。

## @AssertTrue 方法

1. 在参数类中定义方法一个方法用于验证，返回类型必须是 Boolean/boolean,访问权限不能为 private，参数为空，方法名最好是 is 开头，便于阅读,下面是我定义的一个互斥校验：

```java
@AssertTrue(message = "输入时间不合法，请检查！")
    public boolean isDateOrTimeRangeValid() {
        boolean hasDate = date != null;
        boolean hasStart = startTime != null;
        boolean hasEnd = endTime != null;

        // 校验互斥逻辑
        if (hasDate || (hasStart && hasEnd)) {
            // 只要满足要么date不为null，要么start和end同时存在，就返回true
            if (hasDate) {
                this.startTime = date.atStartOfDay();
                this.endTime = date.atTime(LocalTime.MAX);
                return true;
            } else {
                return startTime.isBefore(endTime);
            }
        }
        return false;
    }
```

2. 在开 controller 层开启校验@Valid

```java
public R<List<StatsResultVO>> queryStats(@RequestBody @Valid StatsQueryVO vo)
```

## 后续待改进

目前这个还是不能自定义返回消息，就是在方法内部设置一个返回消息，表示一个具体信息，只能通过抛出异常的方式来实现。

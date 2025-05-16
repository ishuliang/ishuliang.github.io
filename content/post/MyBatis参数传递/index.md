---
title: Mybatis参数传递
description: "@Param注解用法"
date: 2024-07-28 09:40:03
image: 20240728.jpg
slug: /20240728
---

## MyBatis多参数传递 四种情况需要加@Param
1. 方法有多个参数，需要使用@Param注解。
2. 当需要给参数取别名时，需要使用@Param注解。
3. XML中的SQL使用了`$`,参数中也需要使用@Param。
4. 动态 SQL ，如果在动态 SQL 中使用了参数作为变量，那么也需要 @Param 注解，即使你只有一个参数。

```java
//mapper 接口
List<Device> getDeviceListTest(@Param("deviceId") String deviceId);
<!--mybatis的xml-->
<select id="getDeviceListTest" parameterType="String" resultType="Device">
        select * from t_device
        <where>
           <if test="deviceId != null and deviceId != ''">
               device_id=#{deviceId}
           </if>
        </where>
    </select>
```

## 为什么有时候不加@Param可以正常运行

是因为编译的问题，有时候接口通过编译，参数名称发生了变化，所以会报错找不到该参数，有时候又可以正常运行是因为idea编译采用了**强制保持方法参数变量明**

参考文章，这篇解释的详细一点：https://blog.csdn.net/neusoft2016/article/details/110818507

---
title: "Java常用工具类代码"
description: 常用的java代码，记录一下
date: 2024-07-23 17:20:49
image: 20240723.jpg
slug: /20240723
categories: "work"
---

## 常用方法

### `ObjectUtil.isEmpty()`

比较笼统的判断是否为空，不属于下面类型的仅仅判断是否为null

```java
    public static boolean isEmpty(Object obj) {
        if (null == obj) {
            return true;
        }

        if (obj instanceof CharSequence) {
            return StrUtil.isEmpty((CharSequence) obj);
        } else if (obj instanceof Map) {
            return MapUtil.isEmpty((Map) obj);
        } else if (obj instanceof Iterable) {
            return IterUtil.isEmpty((Iterable) obj);
        } else if (obj instanceof Iterator) {
            return IterUtil.isEmpty((Iterator) obj);
        } else if (ArrayUtil.isArray(obj)) {
            return ArrayUtil.isEmpty(obj);
        }

        return false;
    }
```

### `Optional.ofNullable().map().orElse()`

`v`指代是`gms`这个列表

```java 
List<AllErgenInfoResp> gms = wsAdpterService.getPatientGmInfo(hospitalNumber);
                patientTagVO.setIsGm(
                        Optional.ofNullable(gms)
                                .map(v -> !v.isEmpty())
                                .orElse(false)
                );
```

### equal比较

使用`("gcx_blood").equals(param.getObsvCode())`而不是`param.getObsvCode().equals()`

> 这样写可以避免空指针异常，如果`param.getObsvCode()`返回`null`。调用`equal()`会抛异常，而使用`("gcx_blood").equals(null)` 不会抛出异常，而是直接返回 `false`。

## `LocalDateTime`用法

### 格式化输出

```java
package cn.hutool.core.date;
	/**
	 * 格式化日期时间为yyyy-MM-dd HH:mm:ss格式
	 *
	 * @param time {@link LocalDateTime}
	 * @return 格式化后的字符串
	 * @since 5.3.11
	 */
	public static String formatNormal(LocalDateTime time) {
		return format(time, DatePattern.NORM_DATETIME_FORMATTER);
	}
```

### string转LocalDateTime

```java
    /**
     * 将字符串转为LocalDateTime
     *
     * @param dateTimeString
     * @return java.time.LocalDateTime
     * @author 刘书良
     * 2025/1/9 9:34
     **/
    public static LocalDateTime stringToLocalDateTime(String dateTimeString) {
        LocalDateTime localDateTime = LocalDateTimeUtil.parse(dateTimeString, DatePattern.NORM_DATETIME_PATTERN);
        return localDateTime;
    }
```



## 把map中的值转为list

```java
		Map<String, List<String>> patientMap = new HashMap<>();
        patientMap.put("patient1", Arrays.asList("a", "b", "c"));
        patientMap.put("patient2", Arrays.asList("d", "e"));
        patientMap.put("patient3", Arrays.asList("f"));

        // 将所有 values 合并为一个 List
        List<String> combinedList = patientMap.values().stream()
                .flatMap(List::stream) // 将每个 List 展开为单个元素的流
                .collect(Collectors.toList()); // 收集成一个 List
```

## 获取一天的开始和结束时间

```java
LocalDateTime startOfDay = LocalDateTime.now().with(LocalTime.MIDNIGHT).minusMinutes(10);
 LocalDateTime endOfDay = LocalDateTime.now().with(LocalTime.MAX);
```


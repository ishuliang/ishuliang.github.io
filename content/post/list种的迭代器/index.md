---
title: "List迭代器中的modCount"
description: 学习list迭代器
date: 2025-07-08 17:10:00
# image: 20250708.jpg
slug: /20250708
categories: "work"
draft: true
---

# 迭代器

## 使用迭代器遍历删除

```java
	public static void main(String[] args) {
        List<Integer> arrayList = new ArrayList<>((int) (100/0.75));
        for (int i = 0; i < 100; i++) {
            arrayList.add(i);
        }
        Iterator<Integer> it = arrayList.iterator();
        while (it.hasNext()) {
            Integer next = it.next();
            System.out.println(next);
            it.remove();
        }
    }
```

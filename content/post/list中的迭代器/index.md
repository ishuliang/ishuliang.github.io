---
title: "List迭代器"
description: 学习list迭代器
date: 2025-07-08 17:10:00
image: 5a02a09f0f138a8d1d371ce346f04e5.jpg
slug: /20250708
categories: "knowledge"
---

# Iterator迭代器

## 是什么

Iterator 接口提供遍历任何 Collection 的接口。我们可以从一个 Collection 中使用迭代器方法来获取迭代器实例。迭代器取代了 Java 集合框架中Enumeration，迭代器允许调用者在迭代过程中移除元素。

因为所有Collection接继承了Iterator迭代器。

```java
public interface Collection<E> extends Iterable<E>
```

Iterator 的特点是只能单向遍历，但是更加安全，因为它可以确保，在当前遍历的集合元素被更改的时候，就会抛出` ConcurrentModificationException `异常。

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

```java
for(Integer i : list){
list.remove(i)
}

运行以上错误代码会报 ConcurrentModificationException 异常。
这是因为当使用foreach(for(Integer i : list)) 语句时，
会自动生成一个iterator 来遍历该 list，
但同时该 list 正在被Iterator.remove() 修改。
Java 一般不允许一个线程在遍历 Collection 时另一个线程修改它。
```

TODO 之后可以在研究一下为什么

## 使用迭代器修改

```java
List<Integer> arrayList = new ArrayList<>((int) (100/0.75));
        for (int i = 0; i < 100; i++) {
            arrayList.add(i);
        }
        Iterator<Integer> it = arrayList.iterator();
        while (it.hasNext()) {
            Integer next = it.next();
            next = 1000;
        }
        System.out.println(arrayList);
```

没有修改成功：

这段代码的逻辑是：

1.  `it.next()` 拿出的是集合中的某个值的 **副本**（是值，不是引用）
2.  然后你执行了 `next = 1000;` —— 这只是修改了变量 `next` 本身的引用
3.  并没有回写到 `arrayList` 中的那个位置

可使用`ListIterator`

```java
ListIterator<Integer> it = arrayList.listIterator();
while (it.hasNext()) {
    it.next();
    it.set(1000);
}
```

## Iterator 和 ListIterator 有什么区别？

-   `Iterator` 可以遍历 Set 和 List 集合，而` ListIterator `只能遍历 List。
-   `Iterator `只能单向遍历，而` ListIterator `可以双向遍历（向前/后遍历）。
-   `ListIterator` 实现` Iterator `接口，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。

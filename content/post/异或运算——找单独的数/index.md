---
title: "异或运算——找单独的数"
description: 一道算法题目
date: 2024-11-13 15:04:30
image: 20241113.jpg
slug: /20241113
categories: "work"
---

## 异或、与、或都是按位运算。

或运算有两种情况会导致结果为`true`，分别是：1. 都为true；2. 一个为true，一个为false。为了区分这种情况引入了异或`^`，这样或的第一种全相同则为false，而不同则为true，也就是说异或主要用于判断两个值是否为**不同**

## 异或运算规律

由于相同为0，不同为1.

所以：

1. 一个值与自己的异或总为false
2. 一个值与0的异或总为这个数
3. 可交换性、可结合性

## 异或应用方面

1. 简化计算

我们可以先找相同的异或得到0在计算其他的。

2. 交换值

两个变量连续三次异或就可以互换值。

> x, y
>
> 第一次：x1 = x ^ y
>
> 第二次：y1 = x1 ^ y = x ^ y ^ y = x
>
> 第三次：x2 = x1 ^ y1 = x ^ y ^ x = y
>
> 现在x2 = y; y1 = x。

3. 加密、数据备份

参考ruanyf的博客吧（https://www.ruanyifeng.com/blog/2021/01/_xor.html）

## 想到异或的原因

在[豆包上看到一个题目](https://www.marscode.cn/practice/9e08kko47ern5p?problem_id=7414004855077912620)，然后就看了一些别人的写法，觉得不错，记录一下。

原题是这样的：

**在一个班级中，每位同学都拿到了一张卡片，上面有一个整数。有趣的是，除了一个数字之外，所有的数字都恰好出现了两次。现在需要你帮助班长小C快速找到那个拿了独特数字卡片的同学手上的数字是什么。**

输入：cards = [1, 1, 2, 2, 3, 3, 4, 5, 5],输出4，因为4是唯一一个没有配对的

输入：cards = [0, 1, 0, 1, 2]，输出2，因为2是唯一一个没有配对的

约束条件： 数字大小为奇数，重复数字只会出现两次。

```java
public class Main {
    public static int solution(int[] cards) {
        // Edit your code here
        int result = 0;
        for (int card : cards) {
            result ^= card;
        }
        return result;
    }

    public static void main(String[] args) {
        // Add your test cases here
        
        System.out.println(solution(new int[]{1, 1, 2, 2, 3, 3, 4, 5, 5}) == 4);
        System.out.println(solution(new int[]{0, 1, 0, 1, 2}) == 2);
    }
}
```

这里用的就是异或的运算，所有的异或之后，因为重复的数字只会出现两次，所以重复数字计算会为0，单独一个就剩下来了，就输出来了。
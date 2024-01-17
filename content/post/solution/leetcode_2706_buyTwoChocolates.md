---
title: "LeetCode 2706. 购买两块巧克力"
date: 2023-12-29T12:00:34+08:00
draft: false
math: true

tags: ["LeetCode", "数组", "暴力解法", "每日一题"]
categories: ""
description: "2023-12-29 leetcode 每日一题。求出所有巧克力中的价格最小的两个，判断是否满足购买条件，返回依情况返回。"
---

> [LeetCode 2706. 购买两块巧克力](https://leetcode.cn/problems/buy-two-chocolates/)

### 思路

求出所有巧克力中的价格最小的两个，判断是否满足购买条件，返回依情况返回。

### 求解过程

准备两个变量，记录下最小值和次小值。遍历数组，更新最小值和次小值。

1. 如果当前值比最小值小：最小值赋值给次小值，更新最小值。
2. 如果当前值仅比次小值小：更新次小值。
3. 如果当前值比次小值还大：略过。

最后，根据剩余金额返回结果。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int buyChoco(int[] prices, int money) {
        int min1 = Integer.MAX_VALUE, min2 = Integer.MAX_VALUE;
        for (int p : prices) {
            if (p < min1) {
                min2 = min1;
                min1 = p;
            } else if (p < min2) {
                min2 = p;
            }
        }
        int sum = min1 + min2;
        return sum <= money ? money - sum : money;
    }
}
```
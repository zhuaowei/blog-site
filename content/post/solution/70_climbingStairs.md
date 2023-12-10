---
title: "LeetCode 70. 爬楼梯"
date: 2023-12-10T09:34:09+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "每日一题"]
categories: ""
description: "2023-12-10 leetcode 每日一题，非常经典的动态规划。可以一步一步从暴力递归到记忆化搜索，再到动态规划，最后空间压缩。"
---

> [LeetCode 70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

### 思路

爬一个长度为 n 的阶梯，可能从 n - 1 阶跳一步来到 n，也可以从 n - 2 一下跳两步来到 n。所以来到第 i 个台阶的方法数就等于：

$$ f(i) = f(i - 1) + f(i - 2) $$

### 求解过程

可以使用递归的方式，basecase 为 `i == 1 || i == 2`。

递归的方式会有很多重复调用过程，所以使用一个 dp 表记录下调用的结果，如果结果存在直接返回，如果不存在，再展开。这种方式等价与使用一维 DP 表。

如果直接动态规划，发现 `dp[i]` 只依赖前两项 `dp[i - 1] + dp[i - 2]`，所以可以使用滚动数组的方式，记录前两项的值，然后更新当前项，再更新前两项，进入下一次循环。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int climbStairs(int n) {
        if (n < 3) {
            return n;
        }
        int prev = 1, cur = 2, next;
        for (int i = 3; i <= n; i++) {
            next = prev + cur;
            prev = cur;
            cur = next;
        }
        return cur;
    }
}
```
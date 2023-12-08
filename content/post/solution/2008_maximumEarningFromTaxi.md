---
title: "LeetCode 2008. 出租车的最大盈利"
date: 2023-12-08T11:01:10+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "排序", "每日一题"]
categories: ""
description: "2023-12-08 leetcode 每日一题，显示按照起始点进行排序，超时了，看了大佬题解，使用终点排序进行求解，整理一下思路"
---

> [2008. 出租车的最大盈利](https://leetcode.cn/problems/maximum-earnings-from-taxi/)

## 思路

司机在行驶过程中，只能往右走，路程中依次经过 1 -> n，求司机的最大盈利。首先想到动态规划，求解司机在到达 i 点时获得的最大盈利。

司机在行驶中一共有两种状态，一是载客，二是不载客。如果是不载客到达 i 点，那么它的最大盈利等于到达 i - 1 时的最大盈利；如果他是载客状态到达了 i 点，那么它的盈利为 **终点为 i 的订单获得的盈利**加上**到达终点为 i 的订单的起点获得的盈利**。然后需要将二者进行比较，求较大值。

在计算载客状态时，可能会有多个订单的终点在 i 位置，需要一一计算，然后取最大值。

## 求解过程

首先需要将所有订单按照终点进行排序。

然后开始从 1 -> n 进行遍历，计算出不载客的时候，到达 i 位置的最大收益，然后判断是否有到达 i 节点的订单，如果有，计算收益，取最大值。

因为司机是不会回退的，订单终点排好序了，也是不回退的，所以只需要记录订单终点为 i 的订单索引，不必每次进行搜索。

## 复杂度

- 时间复杂度：$O(n + m)$

n 为地点个数，m 为订单个数。虽然有两层循环，但是 n 和 m 只遍历一遍，最多 n + m.

- 空间复杂度：$O(n)$

## 代码

```java
class Solution {
    public static int MAXN = 100005;
    public static long[] dp = new long[MAXN];
    public long maxTaxiEarnings(int n, int[][] rides) {
        // 排序 + 动态规划
        Arrays.fill(dp, 0, n, 0);
        // 按照终点进行排序
        Arrays.sort(rides, 0, rides.length, (r1, r2) -> r1[1] - r2[1]);
        // 终点为 i 的订单索引位置
        int index = 0;
        for (int i = 1; i <= n; i++) {
            // 不载客
            dp[i] = dp[i - 1];
            // 如果有订单，载客
            while (index < rides.length && rides[index][1] <= i) {
                dp[i] = Math.max(dp[i], dp[rides[index][0]] +  rides[index][1] -  rides[index][0] +  rides[index][2]);
                index++;
            }
        }
        return dp[n];
    }
}
```
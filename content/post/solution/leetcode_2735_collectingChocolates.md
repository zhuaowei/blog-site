---
title: "LeetCode 2735. 收集巧克力"
date: 2023-12-28T11:58:29+08:00
draft: false
math: true

tags: ["LeetCode", "数组", "暴力解法", "每日一题"]
categories: ""
description: "2023-12-28 leetcode 每日一题。暴力解法，求出每一个巧克力在每一种移动情况下的最小成本，再加上移动步数的成本，求出每一种方案的最小值。"
---

> [LeetCode 2735. 收集巧克力](https://leetcode.cn/problems/collecting-chocolates/)

### 思路

暴力解法，求出每一个巧克力在每一种移动情况下的最小成本，再加上移动步数的成本，求出每一种方案的最小值。

### 求解过程

使用一个数组记录下所有巧克力在移动至多 $i$ 步的情况下的最小成本，移动步数最多为所有巧克力的个数。先计算出每一步的移动成本，之后再计算收集成本。

遍历每一个巧克力 $nums_i$，计算它至多移动 $j$ 步时的最小成本，将成本累加到 $cost_i$ 中。

计算完所有巧克力在至多移动所有步数的结果后，在所有成本中取最小成本返回。

### 复杂度

- 时间复杂度：$O(n^2)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public long minCost(int[] nums, int x) {
        int n = nums.length;
        long[] cost = new long[n];
        // 移动成本
        for (int i = 1; i < n; i++) {
            cost[i] = cost[i - 1] + x;
        }
        for (int i = 0; i < n; i++) {
            int temp = nums[i];
            // 收集第 i 个巧克力，在移动 j 步的情况下的最小成本
            for (int j = 0; j < n; j++) {
                temp = Math.min(temp, nums[(i - j + n) % n]);
                cost[j] += temp;
            }
        }
        long ans = Long.MAX_VALUE;
        // 所有成本的最小值
        for (long c : cost) {
            ans = Math.min(ans, c);
        }
        return ans;
    }
}
```
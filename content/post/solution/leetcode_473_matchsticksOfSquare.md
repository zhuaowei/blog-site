---
title: "LeetCode 473. 火柴拼正方形"
date: 2024-01-18T22:45:08+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "状压DP"]
categories: ""
description: "依次满足每一条边，直到四条边全部满足，然后判断是否有火柴剩余。每次挑选时，尝试剩下的每一种火柴，看看它以及后续的选择是否存在一个选择方案将四条边全部满足。"
---

> [LeetCode 473. 火柴拼正方形](https://leetcode.cn/problems/matchsticks-to-square/) 
>
> [算法讲解080【必备】状压dp-上](https://www.bilibili.com/video/BV15a4y1o7NA/)

### 思路

依次满足每一条边，直到四条边全部满足，然后判断是否有火柴剩余。每次挑选时，尝试剩下的每一种火柴，看看它以及后续的选择是否存在一个选择方案将四条边全部满足。

### 求解过程

**状态压缩**

根据题意和数据范围，我们要表示所有火柴的每一种状态，而火柴的最大个数为 15，每个火柴有选与不选两种状态，所以一共是 $2 ^ {15}$ 个状态，是可以通过的。因此我们使用一个 `int` 类型的整数表示所有状态，`1` 表示火柴还没有选择过，`0` 表示火柴已经选择过了。例如下面的整数表示 2、5、6、7、9 号火柴还没有被选。

```text
number:  9 8 7 6 5 4 3 2 1 0
int bit: 1 0 1 1 1 0 0 1 0 0
```

**Base Case**

当所有火柴都被选择的时候，递归到达最底层，也即 `status == 0`。这时候判断是否将四条边全部满足，不多也不少，也即 `mat == 4`。

**递归过程**

因为四条边是按顺序逐个满足的，且只要知道了哪些火柴被选，就知道了当前来到了哪条边。因此只需要使用 dp 表记录状态即可，不需要使用其他的纬度。

在选择火柴时，依次选择没有选过的火柴，挑选的火柴分为三种情况：

1. 加上该火柴，边长超了：不用，跳过。
2. 加上该火柴，边长正好：一条边满足，继续下一条边，初始化这条边还需要的长度。
3. 加上该火柴，边长正好：也可以用，继续递归这条边，更新这条边还需要的长度。

**剪枝**

根据题目要求，必须使用所有火柴，且必须正好拼成四条边，所以如果火柴的长度之和不能被 4 整除，那么一定无法拼成。如果可以被 4 整除，那么每条边一定是和除以 4。

### 复杂度

- 时间复杂度：$O(n \times 2 ^ n)$
- 空间复杂度：$O(2 ^ n)$

### 代码

```java
class Solution {
    public boolean makesquare(int[] matchsticks) {
        int sum = 0;
        for (int m : matchsticks) {
            sum += m;
        }
        if (sum % 4 != 0) {
            return false;
        }
        int target = sum / 4;
        int n = matchsticks.length;
        int status = (1 << n) - 1;
        int[] dp = new int[1 << n];
        return process(status, n, target, target, 0, matchsticks, dp);
    }
    public boolean process(int status, int n, int rest, int target, int mat, int[] matchsticks, int[] dp) {
        if (status == 0) {
            return mat == 4;
        }
        if (dp[status] != 0) {
            return dp[status] == 1;
        }
        boolean ans = false;
        for (int i = 0; i < n; i++) {
            if ((status & (1 << i)) != 0) {
                if (rest == matchsticks[i]) {
                    ans |= process(status ^ (1 << i), n, target, target, mat + 1, matchsticks, dp);
                } else if (rest > matchsticks[i]) {
                    ans |= process(status ^ (1 << i), n, rest - matchsticks[i], target, mat, matchsticks, dp);
                }
            }
        }
        dp[status] = ans ? 1 : -1;
        return ans;
    }
}
```
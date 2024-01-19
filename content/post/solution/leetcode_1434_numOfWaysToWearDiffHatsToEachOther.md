---
title: "LeetCode 1434. 每个人戴不同帽子的方案数"
date: 2024-01-19T21:34:54+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "状压DP"]
categories: ""
description: "每个人都要选择一个自己喜欢的帽子，可以枚举每个人选择帽子的可能性，列举出所有的可能性，找出一种满足所有人的方案，如果可以满足，则方案数加一。"
---

> [LeetCode 1434. 每个人戴不同帽子的方案数](https://leetcode.cn/problems/number-of-ways-to-wear-different-hats-to-each-other/)
>
> [算法讲解081【必备】状压dp-下](https://www.bilibili.com/video/BV1Tu4y1g7GU/)

### 思路

每个人都要选择一个自己喜欢的帽子，可以枚举每个人选择帽子的可能性，列举出所有的可能性，找出一种满足所有人的方案，如果可以满足，则方案数加一。

### 求解过程

**状态压缩**

我们要想办法表示出所有的状态，如果要表示帽子的状态，最多有 $2 ^ {40}$ 种状态，状态的数量太多，空间会不够。不过人数不多，最多才 10 个人，每个人都满足和不满足两种状态，最多 $2 ^ {10}$ 种状态。只用人的状态无法确定递归的是否为同一个递归函数，还需要一个纬度，就是帽子的序号。我们从小到大依次枚举帽子的序号，再根据人的状态，我们就可以推断出哪个帽子给了谁，也就知道了当前状态的方案数。

我们使用一个 `int` 类型的整数表示人的状态，1 表示这个人还没有获得满意的帽子，0 代表这个人获得了满意的帽子。同时递归函数调用时，传入一个帽子的序号。

**Base Case**

1. 所有人都获得了满意的帽子 `status == 0` ：方案数加一
2. 帽子的序号已经超过了最大的帽子序号：`cur == m + 1` ：该方案不能满足所有人，方案数加零。

**预处理**

1. 获取帽子的最大序号
2. 求出每个帽子可以满足的人的状态：例如某个帽子可以满足 1、3、5 号人，使用一个整数表示可以满足的人的集合，将 1、3、5 号比特位的值设置为 1。（相或）

**递归展开**

1. 所有人都不选则该帽子：跳过该帽子，求出总方案数。
2. 选择一个人使用该帽子：枚举每一个人，这个人还没获得满意的帽子且这个人喜欢这个帽子，求出该方案的总方案数，累加。

在枚举每一个人的时候，我们可以对枚举进行优化，从该帽子可以满足的人中挑选，看看这个人是否已获得满意的帽子。从预处理的状态数组中，我们可以的到一个整数，它表示该帽子可以满足的人的状态，我们需要取出每一个 `1`，可以使用 `rightOne = (temp & -temp);` 获取最右侧的 `1`，然后将这个 `1` 异或掉 (`temp ^= rightOne;`)，然后继续求下一个右侧的 `1`。
### 复杂度

- 时间复杂度：$O(m \times 2 ^ n)$
- 空间复杂度：$O(m \times 2 ^ n)$

### 代码

```java
class Solution {
    public int MOD = 1000000007;
    public int numberWays(List<List<Integer>> hats) {
        // n 个人
        int n = hats.size();
        // 每一个帽子可以满足人的情况
        int[] list = new int[41];
        int m = 0;
        for (int i = 0; i < n; i++) {
            for (int hat : hats.get(i)) {
                m = Math.max(m, hat);
                list[hat] ^= (1 << i);
            }
        }
        int[][] dp = new int[m + 1][1 << n];
        for (int i = 0; i <= m; i++) {
            Arrays.fill(dp[i], -1);
        }
        // 1 可以选，0 不可选
        return process((1 << n) - 1, 1, n, m, list, dp);
    }
    public int process(int status, int cur, int n, int m, int[] list, int[][] dp) {
        if (status == 0) {
            // 所有人都得到了想要的帽子
            return 1;
        }
        if (cur == m + 1) {
            return 0;
        }
        if (dp[cur][status] != -1) {
            return dp[cur][status];
        }
        int ans = process(status, cur + 1, n, m, list, dp) % MOD;
        // 优化枚举时间
        int temp = list[cur];
        int rightOne;
        while (temp != 0) {
            rightOne = (temp & -temp);
            if ((status & rightOne) != 0) {
                ans = (ans + process(status ^ rightOne, cur + 1, n, m, list, dp)) % MOD;
            }
            temp ^= rightOne;
        }
        // for (int i = 0; i < n; i++) {
        //     if ((status & (1 << i)) != 0 && (list[cur] & (1 << i)) != 0) {
        //         ans = (ans + process(status ^ (1 << i), cur + 1, n, m, list, dp)) % MOD;
        //     }
        // }
        dp[cur][status] = ans;
        return ans;
    }
}
```
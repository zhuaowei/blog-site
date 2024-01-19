---
title: "LeetCode 1655. 分配重复整数"
date: 2024-01-19T21:35:31+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "状压DP"]
categories: ""
description: "根据题意，如果一个数字的个数足够多，那么它可以满足多个订单，如果拆开来处理，维护状态就会变得很复杂，因此我们可以选择将订单进行组合，列举出所有组合，计算出它们需要的数字的个数。"
---

> [LeetCode 1655. 分配重复整数](https://leetcode.cn/problems/distribute-repeating-integers/)
>
> [算法讲解081【必备】状压dp-下](https://www.bilibili.com/video/BV1Tu4y1g7GU/)

### 思路

根据题意，如果一个数字的个数足够多，那么它可以满足多个订单，如果拆开来处理，维护状态就会变得很复杂，因此我们可以选择将订单进行组合，列举出所有组合，计算出它们需要的数字的个数。

### 求解过程

为了让一个数字尽可能地满足更多订单，我们枚举该数字可以满足的所有订单组合，判断是否存在一种方案能够使所有订单都满足条件。

**预处理**

1. 首先我们要求出数字的总个数。
2. 其次我们要统计出每种数字的个数。（数字不重要，数字的个数才重要，我们只保留个数信息）
3. 预处理出一个数组，它的每一个下标都是一种订单组合状态，求出每个状态所需的数字个数。

**状态压缩**

我们使用一个整型变量表示订单是否被满足。1 代表没有满足；0 代表已满足。只有订单状态无法确定递归函数是否调用过，还需要数字个数的序号。例如当前来到了第 i 个数字个数，订单状态为 status，我们可以利用前面的数字个数来确定数字是如何分配的，这个状态是唯一的，也就相当于确定了递归函数的调用，下次遇到就不需要展开了。

**枚举子集**

递归展开过程中，需要枚举当前订单状态的所有子集，这里有一个套路，记住即可。

```java
for (int j = status; j > 0; j = (j - 1) & status) {}
```

对于每一个子集，如果当前的数字个数可以满足子集的订单要求，且后续的分配中存在所有订单都满足的情况，说明该分配方案可以通过，返回 true。否则跳过当前数字，使用下一个数字进行分配，将结果返回。

### 复杂度

- 时间复杂度：$O(n \times 3 ^ m)$

n 为数字的种类数，m 为订单数。如何得到 $3 ^ m$ ？需要用到 [二项式定理](https://zh.wikipedia.org/wiki/%E4%BA%8C%E9%A1%B9%E5%BC%8F%E5%AE%9A%E7%90%86)。

递归过程中一共用到了 n 中数字，先乘个 n。 按照订单的个数来分：

- 有 0 个订单的组合数为 $\tbinom{m}{0} = C_m^0$，它的子集个数为 $2 ^ 0$。
- 有 1 个订单的组合数为 $\tbinom{m}{1} = C_m^1$，它的子集个数为 $2 ^ 1$。
- 有 2 个订单的组合数为 $\tbinom{m}{2} = C_m^2$，它的子集个数为 $2 ^ 2$。
- 有 i 个订单的组合数为 $\tbinom{m}{i} = C_m^i$，它的子集个数为 $2 ^ i$。
- 有 m 个订单的组合数为 $\tbinom{m}{m} = C_m^m$，它的子集个数为 $2 ^ m$。

$$
\begin{equation}
\begin{aligned}
    3 ^ m  &= (1 + 2) ^ m\\\\
    & = \sum_{i = 0}^{m}\tbinom{m}{i} \times 2 ^ i\\\\
    &= \tbinom{m}{0} \times 2 ^ 0 + \tbinom{m}{1} \times 2 ^ 1 + \tbinom{m}{2} \times 2 ^ 2 + ... + \tbinom{m}{m} \times 2 ^ m
\end{aligned}
\end{equation}
$$

- 空间复杂度：$O(n \times 2 ^ m)$

### 代码

```java
class Solution {
    public boolean canDistribute(int[] nums, int[] quantity) {
        Arrays.sort(nums);
        int n = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[i - 1]) {
                n++;
            }
        }
        int[] count = new int[n];
        int index = 0;
        count[0] = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[i - 1]) {
                index++;
            }
            count[index]++;
        }
        int m = quantity.length;
        int[] sum = new int[1 << m];
		// 下面这个枚举是生成quantity中的每个子集，所需要数字的个数
		for (int i = 0, v, h; i < quantity.length; i++) {
			v = quantity[i];
			h = 1 << i;
			for (int j = 0; j < h; j++) {
				sum[h | j] = sum[j] + v;
			}
		}
        int[][] dp = new int[1 << m][n];
        return process((1 << m) - 1, 0, n, m, count, sum, dp);
    }
    public boolean process(int status, int cur, int n, int m, int[] count, int[] sum, int[][] dp) {
        if (status == 0) {
            return true;
        }
        if (cur == n) {
            return false;
        }
        if (dp[status][cur] != 0) {
            return dp[status][cur] == 1;
        }
        boolean ans = false;
        int k = count[cur];
        for (int j = status; j > 0; j = (j - 1) & status) {
            if (sum[j] <= k && process(status ^ j, cur + 1, n, m, count, sum, dp)) {
                ans = true;
                break;
            }
        }
        if (!ans) {
            ans = process(status, cur + 1, n, m, count, sum, dp);
        }
        dp[status][cur] = ans ? 1 : -1;
        return ans;
    }
}
```
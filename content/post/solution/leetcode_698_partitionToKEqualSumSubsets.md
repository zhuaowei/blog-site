---
title: "LeetCode 698. 划分为k个相等的子集"
date: 2024-01-18T22:46:34+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "状压DP"]
categories: ""
description: "一个一个子集的满足，直到所有的数字都使用完，最终判断结果满足条件的子集的个数是否为 k。"
---

> [LeetCode 698. 划分为k个相等的子集](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/)
>
> [算法讲解080【必备】状压dp-上](https://www.bilibili.com/video/BV15a4y1o7NA/)

### 思路

这题与 [LeetCode 473. 火柴拼正方形](/article/leetcode-473.-火柴拼正方形/) 十分相似，只是将固定的 4 改成了变量 k 而已。

沿用上面这题的思路，还是一个一个子集的满足，直到所有的数字都使用完，最终判断结果满足条件的子集的个数是否为 k。

### 求解过程

**状态压缩**

使用一个整数表示数字已选和未选的状态，每次递归时，枚举每一个可选的数字，查看该选择能否满足题目的要求，如果存在满足条件的情况，返回 `true`，否则返回 `false`。例如下面的整数表示 2、5、6、7、9 下标的数字还没有被选。

```text
number:  9 8 7 6 5 4 3 2 1 0
int bit: 1 0 1 1 1 0 0 1 0 0
```

**Base Case**

当所有数字都被选择完的时候，也即 `status == 0` 的时候，递归结束，判断满足子集的个数是否为 `k`。

**递归过程**

按照顺序，每次挑选数字只为满足当前子集，只有当前子集被满足了，才能继续下一个子集。同时递归参数要传入当前子集剩余的还需要满足的数字之和。

在选择数字时，依次选择没有选择过的数字，挑选的数字分为三种情况：

1. 加上该数字，子集之和超了：不用，跳过。
2. 加上该数字，子集之和正好：当前子集满足，继续下一个子集，初始化下一个子集还需要的数字之和。
3. 加上该数字，子集之和不够：也可以用，继续递归这个子集，更新这个子集还需要的数字之和。

**剪枝**

根据题目要求，必须使用所有数字，且必须正好划分为 $k$ 个子集，所以如果数组之和不能被 $k$ 整除，那么一定正好划分 $k$ 个满足条件的子集。如果可以被 k 整除，那么每个子集之和肯定为 $\frac{sum}{k}$。

### 复杂度

- 时间复杂度：$O(n \times 2 ^ n)$
- 空间复杂度：$O(2 ^ n)$

### 代码

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        if (sum % k != 0) {
            return false;
        }
        int target = sum / k;
        int n = nums.length;
        int status = (1 << n) - 1;
        int[] dp = new int[1 << n];
        return process(status, n, target, target, 0, k, nums, dp);
    }
    public boolean process(int status, int n, int rest, int target, int cur, int total, int[] nums, int[] dp) {
        if (status == 0) {
            return cur == total;
        }
        if (dp[status] != 0) {
            return dp[status] == 1;
        }
        boolean ans = false;
        for (int i = 0; i < n; i++) {
            if ((status & (1 << i)) != 0) {
                if (rest == nums[i]) {
                    ans |= process(status ^ (1 << i), n, target, target, cur + 1, total, nums, dp);
                } else if (rest > nums[i]) {
                    ans |= process(status ^ (1 << i), n, rest - nums[i], target, cur, total, nums, dp);
                }
            }
        }
        dp[status] = ans ? 1 : -1;
        return ans;
    }
}
```
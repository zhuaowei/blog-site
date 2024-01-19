---
title: "LeetCode 464. 我能赢吗"
date: 2024-01-18T22:43:46+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "状压DP"]
categories: ""
description: "在玩家1的每一个回合都考虑一下剩下数字的所有可能，只要存在获胜的分支，就说明玩家1可以稳赢。"
---

> [LeetCode 464. 我能赢吗](https://leetcode.cn/problems/can-i-win/)
>
> [算法讲解080【必备】状压dp-上](https://www.bilibili.com/video/BV15a4y1o7NA/)

### 思路

在玩家1的每一个回合都考虑一下剩下数字的所有可能，只要存在获胜的分支，就说明玩家1可以稳赢。同理，轮到玩家2时，就要找到玩家2稳输的分支。因为两位玩家都绝顶聪明，所以轮到每一位玩家时，他们都尽最大努力获胜。

### 求解过程

递归子问题以玩家1的视角进行，如果要玩家1获胜，那么玩家1必须在剩下的数字中挑选一个，挑完之后的数字让玩家2选，使得玩家2必输，那么玩家1就可以获胜。

**状态压缩**

因为要考虑每一种情况，所以必须使用一个数据结构来表示哪些数字是挑选过的，哪些是没有被挑选的。通过题目给定的 $n$ 的范围，推断出这个题目应该使用状态压缩DP。因为要表示所有状态，每个数字都有选或者不选两种状态，那么一共就是 $2 ^ n$ 个状态，而 $n$ 的最大值为 $20$，所以状态最大值就约等于 $10 ^ 6$，是可以通过的。

我们使用一个 `int` 类型的整数就可以表示所有的状态，使用整数的比特位作为状态，`1` 表示没有选，还可以选；`0` 表示已经选过了，不能选了。例如下面的示例，2、5、6、7、9 没有选过，其余都选过了。

```text
number:  9 8 7 6 5 4 3 2 1 0
int bit: 1 0 1 1 1 0 0 1 0 0
```

**Base Case**

当递归来到某位玩家时， `rest` 小于等于 0 说明对方已经先一步将总和加到了目标值，该玩家输了；否则说明还有机会，轮到该玩家挑选数字。

**剪枝**

1. 如果目标值是 0，玩家1先手，不用挑数字，直接判定玩家1获胜。
2. 如果所有数字之和都不大于目标值，说明不存在赢家。题目说玩家1稳赢时返回 `true`，这种情况玩家1无法获胜，返回 `false`。

### 复杂度

- 时间复杂度：$O(n \times 2 ^ n)$
- 空间复杂度：$O(2 ^ n)$

### 代码

```java
class Solution {
    public boolean canIWin(int n, int m) {
        if (m == 0) {
            return true;
        }
        if ((n * (n + 1) / 2) < m) {
            return false;
        }
        int[] dp = new int[1 << (n + 1)];
        return process((1 << (n + 1)) - 1, m, n, dp);
    }
    // A 选手是先手，当前状态是 status，剩余 rest 需要选
    public boolean process(int status, int rest, int n, int[] dp) {
        if (rest <= 0) {
            // 对手已经先一步选择
            return false;
        }
        if (dp[status] != 0) {
            return dp[status] == 1;
        }
        boolean ans = false;
        for (int i = 1; i <= n; i++) {
            if ((status & (1 << i)) != 0 && !process((status ^ (1 << i)), rest - i, n, dp)) {
                // i 可以选择。
                ans = true;
                break;
            }
        }
        dp[status] = (ans ? 1 : -1);
        return ans;
    }
}
```
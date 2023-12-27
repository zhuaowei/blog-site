---
title: "LeetCode 2660. 保龄球游戏的获胜者"
date: 2023-12-27T22:42:51+08:00
draft: false
math: true

tags: ["LeetCode", "数组", "每日一题"]
categories: ""
description: "2023-12-27 leetcode 每日一题。按顺序遍历数组，如果某位置的前两次得分存在 10 分，则将本次得分乘 2，分别得到两位玩家的得分，根据得分情况决定胜者。"
---

> [LeetCode 2660. 保龄球游戏的获胜者](https://leetcode.cn/problems/determine-the-winner-of-a-bowling-game/)

### 思路

按顺序遍历数组，如果某位置的前两次得分存在 10 分，则将本次得分乘 2，分别得到两位玩家的得分，根据得分情况决定胜者。

### 求解过程

1. 使用两个变量 $sum1, sum2$ 分别记录两位玩家的得分，使用两个变量 $flag1, flag2$ 分别记录两位玩家最后一次得 10 分的位置。
2. 如果 $flag1 + 2 \ge i$，说明玩家1在 $i$ 位置前两次得分存在 10 分，将本次的得分乘 2。同理可以计算出玩家 2 的得分。
3. 如果本次得分为 10 分，记录下位置。

$flag$ 初始值设置为 -3 是为了保证在 0 位置时，不会错误计算玩家的得分。

### 复杂度

- 时间复杂度：$O(1)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int isWinner(int[] player1, int[] player2) {
        int n = player1.length;
        int sum1 = 0, sum2 = 0;
        int flag1 = -3, flag2 = -3; // 记录下上次分数为 10 的位置
        for (int i = 0; i < n; i++) {
            sum1 += player1[i];
            if (flag1 + 2 >= i) { // 如果在分数为10的位置在前两轮，再加一遍
                sum1 += player1[i];
            }
            if (player1[i] == 10) { // 记录下分数为 10 的位置
                flag1 = i;
            }
            // 玩家 2 的计算过程，与 1 相同
            sum2 += player2[i];
            if (flag2 + 2 >= i) {
                sum2 += player2[i];
            }
            if (player2[i] == 10) {
                flag2 = i;
            }
        }
        if (sum1 > sum2) {
            return 1;
        } else if (sum2 > sum1) {
            return 2;
        } else {
            return 0;
        }
    }
}
```
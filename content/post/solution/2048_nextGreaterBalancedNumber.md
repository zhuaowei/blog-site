---
title: "LeetCode 2048. 下一个更大的数值平衡数"
date: 2023-12-09T09:50:59+08:00
draft: false
math: true

tags: ["LeetCode", "DFS", "暴力解法", "每日一题"]
categories: ""
description: "2023-12-07 leetcode 每日一题，纯暴力解法，遍历每一个可能组成平衡数的值，取满足条件的最小值。由于题目限制，最终结果的位数并不大，再通过剪枝，进一步降低时间复杂度，所以时间复杂度也不是很大。"
---

> [2048. 下一个更大的数值平衡数](https://leetcode.cn/problems/next-greater-numerically-balanced-number/)

## 思路

根据题意求平衡数，由于对结果数字和位数进行了限制，所以能形成平衡数的数字并不是特别多，可以使用打表的方式，将可能的结果存储到一个数组中，再通过二分加速查找。

我使用的方式是暴力求解。根据题意，n 的范围是 $0 <= n <= 10^6$，那么结果最大就是 7 位数 1224444。**长度为 `<= 7` 的平衡数最多由 3 种数字组成，而它们的和不会超过 7**。所以我们可以枚举每一种数字出现的可能，通过这些数字组成一个平衡树，判断是否符合要求，在符合要求的平衡数中取最小值。

## 求解过程

最多有三种数字，每种数字最大为 7，所以使用 3 层 for 循环，枚举每一种数字的可能性。数字为 0 代表它不参与平衡数的组成。

然后写一个递归函数，对每一种可能性的数字组合组成平衡数，将得到的平衡数取 >n 的最小值。

**剪枝**

并不是所有数字组合都可以组成平衡数，数字之间有一定的关联关系：
1. 三数之和不能超过 7。三数之和也就是平衡数的位数，结果最大为 7 位数。
2. 任意两个不为 0 数字不能相等。数字为 0 不参与平衡数组成，不为 0 说明参与组成，如果两个数字相等，则组成的数中该数字个数不为其自身，也就不是平衡数。
3. 由于三种数字是等价的，不同的组合也会得到同样的结果。例如 (2, 0, 0) (0, 2, 0) 等，会重复计算，由于太复杂了，就不进行判断了。

通过上面的分析，最终使用下面的判断语句实现剪枝。

## 复杂度

- 时间复杂度：$O(N \times M)$

N 为循环次数，最多循环 $7 \times 7 \times 7$ 次。M 为每次循环中递归调用次数，最多 $3 \times 4$ 次。

- 空间复杂度：$O(1)$

实际跑下来只比打表慢一点。

## 代码

```java
class Solution {
    int ans = Integer.MAX_VALUE;
    public int nextBeautifulNumber(int n) {
        // 结果最多七位数
        for (int i = 0; i <= 7; i++) {
            for (int j = 0; j <= 7; j++) {
                for (int k = 0; k <= 7; k++) {
                    if (i + j + k <= 7) {
                        if (i == 0 && j != k) {
                            dfs(i, j, k, i, j, k, 0, n);
                            continue;
                        }
                        if (j == 0 && i != k) {
                            dfs(i, j, k, i, j, k, 0, n);
                            continue;
                        }
                        if (k == 0 && i != j) {
                            dfs(i, j, k, i, j, k, 0, n);
                            continue;
                        }
                        if (i != j && j != k && i != k) {
                            dfs(i, j, k, i, j, k, 0, n);
                        }
                    } else {
                        break;
                    }
                }
            }
        }
        return ans;
    }
    // 在使用 cn1 个 n1 ，cn2 个 n2，cn3 个 n3 时，组成的 num 能否 > target
    public void dfs(int n1, int n2, int n3, int cn1, int cn2, int cn3, int num, int target) {
        if (cn1 == 0 && cn2 == 0 && cn3 == 0) {
            if (num > target) {
                ans = Math.min(ans, num);
            }
            return;
        }
        // 先选 n1
        if (cn1 > 0) {
            dfs(n1, n2, n3, cn1 - 1, cn2, cn3, num * 10 + n1, target);
        }
        // 先选 n2
        if (cn2 > 0) {
            dfs(n1, n2, n3, cn1, cn2 - 1, cn3, num * 10 + n2, target);
        }
        // 先选 n3
        if (cn3 > 0) {
            dfs(n1, n2, n3, cn1, cn2, cn3 - 1, num * 10 + n3, target);
        }
    }
}
```
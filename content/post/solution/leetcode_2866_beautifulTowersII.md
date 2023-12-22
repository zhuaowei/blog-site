---
title: "LeetCode 2866. 美丽塔 II"
date: 2023-12-21T14:43:24+08:00
draft: false
math: true

tags: ["LeetCode", "单调栈", "每日一题"]
categories: ""
description: "2023-12-21 leetcode 每日一题。根据题意，需要在高度限制下建塔，使得最终的塔能够形成一个山脉的形状。山脉的形状就是，从两侧到山顶是非严格递增的。所以我们可以假设每一个位置作为山顶，求出它左侧非严格递增，右侧非严格递减的子数组之和的最大值。"
---

> [LeetCode 2866. 美丽塔 II](https://leetcode.cn/problems/beautiful-towers-ii/)

### 思路

根据题意，需要在高度限制下建塔，使得最终的塔能够形成一个山脉的形状。山脉的形状就是，从两侧到山顶是非严格递增的。所以我们可以假设每一个位置作为山顶，求出它左侧非严格递增，右侧非严格递减的子数组之和的最大值。

### 求解过程

先求出从 $0$ 到 $i$ 位置，以 $i$ 为山顶的非严格递增子数组的最大和。然后逆序遍历，求出从 $n - 1$ 到 $i$ 位置的以 $i$ 为山顶的非严格递增子数组的最大和，二者相加再去掉重复的顶点，就是以 $i$ 为山顶的山脉之和。

在求以 $i$ 为山脉的左侧最大和时，使用单调栈求解。假设当前在 $i$ 位置，左侧比它小的位置是 $j$，那么在 $[j + 1, i]$ 这段只能全部为 $nums_i$，而 $[0, j]$ 范围的最大和为 $pre_j$，那么 $i$ 位置左侧的最大和就是 $pre_j + (i - j + 1) \times nums_i$。

同理可以求出右侧以 $i$ 为山脉的最大和，那么 $pre_i + sub_i - nums_i$ 即为以 $i$ 为山顶的山脉之和。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public long maximumSumOfHeights(List<Integer> maxHeights) {
        int n = maxHeights.size();
        int[] stack = new int[n];
        int r = 0;
        long[] pre = new long[n];
        long[] sub = new long[n];
        // 求出从 0 到 i 能组成的最大的非降序数组（必须以 i 为最高点）
        for (int i = 0; i < n; i++) {
            while (r > 0 && maxHeights.get(i) <= maxHeights.get(stack[r - 1])) {
                r--;
            }
            long p = r > 0 ? pre[stack[r - 1]] : 0;
            int left = r > 0 ? stack[r - 1] : -1;
            pre[i] = p + (long)maxHeights.get(i) * (i - left);
            stack[r++] = i;
        }
        r = 0;
        long ans = 0;
        // 求出从 i 到 n - 1 能组成的最大的非升序数组（必须以 i 为最高点）
        for (int i = n - 1; i >= 0; i--) {
            while (r > 0 && maxHeights.get(i) <= maxHeights.get(stack[r - 1])) {
                r--;
            }
            long s = r > 0 ? sub[stack[r - 1]] : 0;
            int right = r > 0 ? stack[r - 1] : n;
            sub[i] = s + (long)maxHeights.get(i) * (right - i);
            ans = Math.max(ans, pre[i] + sub[i] - maxHeights.get(i));
            stack[r++] = i;
        }
        return ans;
    }
}
```
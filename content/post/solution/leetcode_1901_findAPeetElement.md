---
title: "LeetCode 1901. 寻找峰值 II"
date: 2023-12-19T22:00:58+08:00
draft: false
math: true

tags: ["LeetCode", "二分", "每日一题"]
categories: ""
description: "2023-12-19 leetcode 每日一题。使用二分，根据上山路径的方向，不断缩小区间的范围，确定峰值所在的行。"
---

> [LeetCode 1901. 寻找峰值 II](https://leetcode.cn/problems/find-a-peak-element-ii/)
>
> 参考题解：[灵茶山艾府 - 【图解】利用行最大值判断峰顶位置，附二分题单](https://leetcode.cn/problems/find-a-peak-element-ii/solutions/2571587/tu-jie-li-yong-xing-zui-da-zhi-pan-duan-r4e0n/?envType=daily-question&envId=2023-12-19)

### 思路

根据 “上山” 的思路，我们只需要沿着“往上走的方向”即可找到一个峰值，即我们每次往上下左右比自己大的位置跳，直到周围的值都比自己小。这种情况最坏情况下时间复杂度是 $O(nm)，使用二分，将上山的路径进行划分，峰值肯定在中点的一侧，或者中点上。

### 求解过程

假设存在一条上山路径，我们以行进行二分，我们求出中间行的最大值，则这个最大值肯定在去往峰值的路径上。

在 $[i, j]$ 行范围上二分，中间行的最大值 $nums_{mid}$ 将路径划分为两部分，$\{< nums_{mid}, > nums_{mid}\}$，我们可以根据最大值上下两个格子的大小继续判断大于部分在那个区间，然后逐渐缩小区间，求出峰值所在的行，而这行的最大值就是峰值。

### 复杂度

- 时间复杂度：$O(n \times log_2n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int[] findPeakGrid(int[][] mat) {
        int n = mat.length, m = mat[0].length;
        int left = 0, right = n - 2;
        while (left <= right) {
            int i = left + (right - left) / 2;
            int j = indexOfMaxValue(mat[i]);
            if (mat[i][j] > mat[i + 1][j]) {
                right = i - 1;
            } else {
                left = i + 1;
            }
        }
        return new int[] {left, indexOfMaxValue(mat[left])};
    }

    public int indexOfMaxValue(int[] nums) {
        int ans = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[ans] < nums[i]) {
                ans = i;
            }
        }
        return ans;
    }
}
```
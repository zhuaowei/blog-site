---
title: "LeetCode 2132. 用邮票贴满网格图"
date: 2023-12-14T16:19:47+08:00
draft: false
math: true

tags: ["LeetCode", "二维前缀和", "二维差分", "每日一题"]
categories: ""
description: "2023-12-14 leetcode 每日一题，遍历每一个可以作为邮票右下角的点，判断是否可以贴邮票，如果可以，使用差分数组将邮票大小的矩阵加一，之后还原为每个格子邮票的个数，如果存在格子没有被占据且邮票数量为 0，返回 false。"
---

> [LeetCode 2132. 用邮票贴满网格图](https://leetcode.cn/problems/stamping-the-grid/)

### 思路

遍历每一个邮票右下角可能符合要求的点，判断在邮票范围内是否存在被占据的点。如果存在，什么都不做；如果不存在，可以贴邮票，将邮票范围内的子矩阵整体加一，这里使用二维差分来实现。

遍历完成后，将标记好的数组使用前缀和转化为表示邮票个数的数组。然后遍历数组中的每一个格子，如果某格子没有被占据，也不存在邮票，则返回 false。最终返回 true。

### 求解过程

先统计网格的二维前缀和数组。然后遍历每一个可以作为邮票右下角的点：

1. 如果邮票范围内的格子存在被占据的情况，不能放邮票。
2. 否则，放置邮票，通过差分将范围内的邮票数量加一。

最终还原每个格子的邮票数量，如果某个格子没有被占据，且邮票数量为 0，返回false。最终，不存在空格子没邮票，返回true。

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(n \times m)$

### 代码

```java
class Solution {
    public boolean possibleToStamp(int[][] grid, int stampHeight, int stampWidth) {
        int n = grid.length, m = grid[0].length;
        // if (stampHeight > n || stampWidth > m) {
        //     return false;
        // }
        int[][] sums = new int[n + 1][m + 1];
        int[][] res = new int[n + 1][m + 1];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                sums[i + 1][j + 1] = grid[i][j] + sums[i + 1][j] + sums[i][j + 1] - sums[i][j];
            }
        }
        for (int i = stampHeight; i <= n; i++) {
            for (int j = stampWidth; j <= m; j++) {
                int cnt = sums[i][j] 
                    - sums[i][j - stampWidth] 
                    - sums[i - stampHeight][j]
                    + sums[i - stampHeight][j - stampWidth];
                // 如果可以放下
                if (cnt == 0) {
                    res[i - stampHeight + 1][j - stampWidth + 1] += 1;
                    if (i < n) {
                        res[i + 1][j - stampWidth + 1] -= 1;
                    }
                    if (j < m) {
                        res[i - stampHeight + 1][j + 1] -= 1;
                    }
                    if (i < n && j < m) {
                        res[i + 1][j + 1] += 1;
                    }
                }
            }
        }
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                res[i + 1][j + 1] += res[i + 1][j] + res[i][j + 1] - res[i][j];
                if (grid[i][j] == 0 && res[i + 1][j + 1] == 0) {
                    return false;
                }
            }
        }
        return true;
    }
}
```
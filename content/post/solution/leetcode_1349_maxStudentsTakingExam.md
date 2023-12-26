---
title: "LeetCode 1349. 参加考试的最大学生数"
date: 2023-12-26T20:51:42+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "状压DP", "每日一题"]
categories: ""
description: "2023-12-26 leetcode 每日一题。定义子问题：在前 n 排的情况下，如何安排才能使坐下的学生最多。计算当前排如何安排时，遍历每一种可能的方案，对于每一种方案，遍历上一行的每一种方案，求每一种安排方案的最大值。"
---

> [LeetCode 1349. 参加考试的最大学生数](https://leetcode.cn/problems/maximum-students-taking-exam/)
>
> [腐烂的橘子 - 详解 压缩状态动态规划解法](https://leetcode.cn/problems/maximum-students-taking-exam/solutions/90312/xiang-jie-ya-suo-zhuang-tai-dong-tai-gui-hua-jie-f)
>
> [灵茶山艾府 - 教你一步步思考动态规划：从记忆化搜索到递推（两种状态定义+题单）](https://leetcode.cn/problems/maximum-students-taking-exam/solutions/2580043/jiao-ni-yi-bu-bu-si-kao-dong-tai-gui-hua-9y5k)

### 思路

定义子问题：在前 n 排的情况下，如何安排才能使坐下的学生最多。计算当前排如何安排时，遍历每一种可能的方案，对于每一种方案，遍历上一行的每一种方案，求每一种安排方案的最大值。

### 求解过程

**状态压缩**

1. 教室的座位状态压缩：教室中的椅子分为好的和坏的，只能在好的椅子上安排学生。记好的椅子为 1，坏的椅子为 0，因为每一排椅子最多有 8 个，所以可以使用一个整数来表示。那么 n 排椅子可以使用一个 n 大小的一维数组表示。
2. 安排方案的状态压缩：每一排的好的椅子，可以安排坐学生，也可以不坐。记坐学生为 1，不坐学生为 0。同样使用一个整数表示一排学生的安排方案，最多有 8 个位子，所以最多有 $2 ^ 8$ 种安排方案。

教室座位和安排方案的状态压缩存在一种关系，就是安排方案是包含于教室座位的，也就是说，我们只能在教室座位是好的中选择，$schema \subseteq status_i$。

**状态转移**

子问题是在前 n 排的座位中，每种安排方案的最大学生数。如何从上一行的安排方案中得到本行的安排方案？对于本行每一种的安排方案，遍历上一行所有的安排方案，如果两种方案并不冲突，都可以坐下，那么就可以计算出这种方案的人数。

$$
\begin{equation}
\begin{aligned}
dp_{i,j} = \begin{cases}
    -1 & 该方案本行都坐不下\\\\
    0 & 该方案本行可以坐下，但是与上一行冲突\\\\
    \max(dp_{i,j}, dp_{i - 1, k} + bitCount(j)) & 可以坐下
\end{cases}
\end{aligned}
\end{equation}
$$

**计算过程**

1. 首先得到教室座位的状态。遍历每一行，使用位运算得到表示每一排状态的整数。
2. 使用 dp 表记录每一排，每一种方案的最大人数。然后遍历每一排，每一种方案。
3. 当前来到第 i 排，方案编号为 j（j 表示的状态并不是最终安排的方案，因为坏的位置可能也会安排学生），使用 j 和第 i 行的状态得到安排方案 $schema = j \& status_i$。这样就保证 schema 是 status_i 的子集，虽然可能会重复，但是不会遗漏。
4. 判断 schema 方案是否存在相邻位置安排学生的情况：$(schema \& (schema << 1)) == 0$，如果结果为 true，说明没有相邻的情况；否则相邻，结果为 -1。
5. 如果结果为 true，可以继续判断。遍历所有上一行的方案，如果本行的座位存在上一行左上或右上已经安排的情况，直接跳过；否则记录更新最大人数。
6. 循环 3 4 5 步骤，得到前 n 排的每种方案的最大安排人数。遍历前 n 排的每一种方案的人数，取最大值返回。

**优化**

1. 遍历本行的方案时，可以使用一个一维数组记录可行的方案编号，这样遍历下一行时，就不用遍历上一行的每种方案编号了。
2. 遍历本行时，只会用到上一行的结果，所以只使用两行 dp 表就可以完成状态转移了。

### 复杂度

- 时间复杂度：$O(N \times 2^{2M})$

N 为教室座位的排数，M 为每排座位的椅子数。

- 空间复杂度：$O(N \times 2^{M})$

### 代码

```java
public class Solution {
    public int maxStudents(char[][] seats) {
        int n = seats.length, m = seats[0].length;
        int[] status = new int[n + 1];
        // 找到可以坐的位置
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < m; j++) {
                if (seats[i - 1][j] == '.') {
                    status[i] |= (1 << j);
                }
            }
        }
        int[][] dp = new int[n + 1][1 << m];
        for (int i = 1; i <= n; i++) {   // 每一排
            for (int s = 0; s < (1 << m); s++) {    // 每一个可能的安排
                int schema = (s & status[i]);       // 在可以选择的座位中选择。
                if ((schema & (schema << 1)) == 0) { // 如果安排的座位没有相邻的，是一种合理安排
                    // 找到上一排座位的安排
                    for (int last = 0; last < (1 << m); last++) {
                        if (dp[i - 1][last] == -1) {
                            continue;
                        }
                        // 如果当前行的所有座位，左上和右上都没有坐人
                        if (((schema << 1) & last) == 0 && ((schema >> 1) & last) == 0) {
                            dp[i][s] = Math.max(dp[i][s], dp[i - 1][last] + countBits(schema));
                        }
                    }
                } else {
                    dp[i][s] = -1;
                }
            }
        }
        int ans = 0;
        for (int i = 0; i < (1 << m); i++) {
            if (ans < dp[n][i]) {
                ans = dp[n][i];
            }
        }
        return ans;
    }
    public int countBits(int num) {
        num = ((num >>> 1) & 0x55555555) + (num & 0x55555555);
        num = ((num >>> 2) & 0x33333333) + (num & 0x33333333);
        num = ((num >>> 4) & 0x0f0f0f0f) + (num & 0x0f0f0f0f);
        num = ((num >>> 8) & 0x00ff00ff) + (num & 0x00ff00ff);
        num = ((num >>> 16) & 0x0000ffff) + (num & 0x0000ffff);
        return num;
    }
}
```
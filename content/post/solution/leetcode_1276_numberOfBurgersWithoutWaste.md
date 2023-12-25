---
title: "LeetCode 1276. 不浪费原料的汉堡制作方案"
date: 2023-12-25T23:03:17+08:00
draft: false
math: true

tags: ["LeetCode", "数学", "每日一题"]
categories: ""
description: "2023-12-25 leetcode 每日一题。设大汉堡 x 个，小汉堡 y 个，根据题意写出二元一次方程组，求出 x 和 y 的解。"
---

> [LeetCode 1276. 不浪费原料的汉堡制作方案](https://leetcode.cn/problems/number-of-burgers-with-no-waste-of-ingredients/)

### 思路

设大汉堡 x 个，小汉堡 y 个，根据题意写出二元一次方程组，求出 x 和 y 的解。

### 求解过程

根据题意，设大汉堡 x 个，小汉堡 y 个，则汉堡个数与原料的关系式为

$$
\begin{equation}
\begin{aligned}
\begin{cases}
    4x + 2y = tomatoSlices\\\\
    x + y = cheeseSlices
\end{cases}
\end{aligned}
\end{equation}
$$

对二元一次方程组进行求解，得到结果：

$$
\begin{equation}
\begin{aligned}
\begin{cases}
    x = \frac{tomatoSlices - 2 \times cheeseSlices}{2}\\\\
    y = cheeseSlices - x
\end{cases}
\end{aligned}
\end{equation}
$$

由于 x y 被赋予了原料数的含义，所以不能为负数和小数。所以当 $x < 0 或 x > cheeseSlices 或 x 为小数$ 时，无法正好使用完原料，返回空数组。

### 复杂度

- 时间复杂度：$O(1)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public List<Integer> numOfBurgers(int tomatoSlices, int cheeseSlices) {
        // a = (t - 2 * c) / 2;
        // b = c - a;
        List<Integer> ans = new ArrayList<Integer>();
        int a = (tomatoSlices - 2 * cheeseSlices);
        if (a < 0 || a > 2 * cheeseSlices || (a & 1) == 1) {
            return ans;
        }
        a /= 2;
        int b = cheeseSlices - a;
        ans.add(a);
        ans.add(b);
        return ans;
    }
}
```
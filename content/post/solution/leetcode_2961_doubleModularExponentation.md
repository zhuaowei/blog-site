---
title: "LeetCode 2961. 双模幂运算"
date: 2023-12-16T21:26:27+08:00
draft: false
math: true

tags: ["LeetCode", "快速幂"]
categories: ""
description: "需要使用快速幂对结果进行计算，将结果与目标值进行对比，如果相等就是好坐标。"
---

> [LeetCode 2961. 双模幂运算](https://leetcode.cn/problems/double-modular-exponentiation/)

### 思路

根据题意，需要对公式进行快速求解，求出类似 $a ^ b \% m$ 的值，因此需要用到快速幂。代码部分附上快速幂的模板（取模和不取模）。

### 求解过程

题本身没什么可说的，说一下快速幂的原理。快速幂可以用于求一个数的大于等于 1 的整数次幂。

**快速幂**

将求幂的式子分解：

$$
\begin{equation}
\begin{aligned}
a^b &= a^{b_0 + b_1 + b_2 + ... + b_i} \\\\
&= a^{b_0} \times a^{b_1} \times ... \times a^{b_i}
\end{aligned}
\end{equation}
$$

b 为一个正整数，它可以分解为二进制数，所以我们可以根据 b 的每一位是否是 1 来决定是否乘与当前的 $a^{k} (k = 0, 1, 2 ...)$。

**取模**

有时候幂的结果很大，无法使用 `int` 或 `long` 来表示，就需要对结果进行取模。根据乘法同余原理，在每一位上计算出结果时，就对结果进行取模，最终的结果与计算完再取模是相等的。

### 复杂度

- 时间复杂度：$O(n \times log_2n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public List<Integer> getGoodIndices(int[][] variables, int target) {
        List<Integer> ans = new ArrayList<>();
        int temp = 0, index = 0;
        for (int[] var : variables) {
            temp = f(f(var[0], var[1], 10), var[2], var[3]);
            if (temp == target) {
                ans.add(index);
            }
            index++;
        }
        return ans;
    }
    public int f(int base, int power, final int mod) {
        long r = base;
        long ans = 1;
        while (power != 0) {
            if ((power & 1) == 1) {
                ans = (ans * r) % mod;
            }
            r = (r * r) % mod;
            power >>= 1;
        }
        return (int) ans;
    }
}
```

```java
// 不带模的快速幂
public static long quickExp(int base, int power) {
    long r = base;
    long ans = 1;
    while (power != 0) {
        if ((power & 1) == 1) {
            ans *= r;
        }
        r *= r;
        power >>= 1;
    }
    return ans;
}
// 带模的快速幂
public static int quickExpWithMod(int base, int power, final int mod) {
    long r = base;
    long ans = 1;
    while (power != 0) {
        if ((power & 1) == 1) {
            ans = (ans * r) % mod;
        }
        r = (r * r) % mod;
        power >>= 1;
    }
    return (int) ans;
}

```
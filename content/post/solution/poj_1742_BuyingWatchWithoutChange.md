---
title: "PKU Judging Online - 1742 Coins"
date: 2023-12-13T17:15:58+08:00
draft: false
math: true

tags: ["PKU Judging Online", "DP", "背包问题", "混合背包"]
categories: ""
description: "这是一道混合背包题目，根据硬币的数量和面值，灵活选择使用 01 背包、完全背包或者多重背包。"
---

> [PKU Judging Online - 1742 Coins](http://poj.org/problem?id=1742)
>
> [算法讲解075【必备】背包dp-多重背包、混合背包](https://www.bilibili.com/video/BV1Nz4y1c71M/)


### 思路

通过不同种类及不同数量的硬币进行组合，判断这些硬币可以组合成 [0, m] 上的哪些值，返回最终的种类数。

这是一道混合背包题目，根据硬币的数量和面值，灵活选择使用 01 背包、完全背包或者多重背包。

### 求解过程

根据硬币的数量，选择不同的解决方式：
1. 如果硬币只有 1 个，使用 01 背包。
2. 如果硬币有 k 个 $(k \times value_i \le m)$，相当于多重背包，使用多重背包来解决。
3. 如果硬币有 k 个 $(k \times value_i \gt m)$，相当于完全背包，用完全背包来解决。

> [ACWing 01背包问题](/article/acwing-01背包问题/)
> 
> [洛谷 P1616 疯狂的采药](/article/洛谷-p1616-疯狂的采药/)
>
> [洛谷 P1776 宝物筛选（单调队列优化）](/article/洛谷-p1776-宝物筛选单调队列优化/)

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(m)$

经过优化，三种背包问题的时间复杂度都是 $O(n \times m)$，空间复杂度都是 $O(m)$。

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 101;
    public static int MAXM = 100001;
    public static int[] value = new int[MAXN];
    public static int[] count = new int[MAXN];
    public static boolean[] dp = new boolean[MAXM];
    public static int n, m;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval;
            in.nextToken();
            m = (int)in.nval;
            if (n == 0 && m == 0) {
                break;
            }
            for (int i = 1; i <= n; i++) {
                in.nextToken(); value[i] = (int)in.nval;
            }
            for (int i = 1; i <= n; i++) {
                in.nextToken(); count[i] = (int)in.nval;
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute() {
        Arrays.fill(dp, 1, m + 1, false);
        dp[0] = true;
        for (int i = 1; i <= n; i++) {
            if (count[i] == 1) {
                // 01 背包
                for (int j = m; j >= value[i]; j--) {
                    if (dp[j - value[i]]) { // 如果选一个，且上次为 true
                        dp[j] = true;
                    }
                }
            } else if (value[i] * count[i] > m) {
                // 完全背包
                for (int j = value[i]; j <= m; j++) {
                    if (dp[j - value[i]]) {
                        dp[j] = true;
                    }
                }
            } else {
                for (int spare = 0; spare < value[i]; spare++) {
                    int cnt = 0;
                    for (int j = m - spare, k = 0; j >= 0 && k <= count[i]; j -= value[i], k++) {
                        if (dp[j]) {
                            cnt++;
                        }
                    }
                    for (int j = m - spare, l = j - (count[i] + 1) * value[i]; j >= 1; j -= value[i], l -= value[i]) {
                        if (dp[j]) {
                            cnt--;
                        } else {
                            dp[j] = cnt > 0;
                        }
                        if (l >= 0 && dp[l]) {
                            cnt++;
                        }
                    }
                }
            }
        }

        int ans = 0;
        for (int j = 1; j <= m; j++) {
            if (dp[j]) {
                ans++;
            }
        }
        return ans;
    }
}
```
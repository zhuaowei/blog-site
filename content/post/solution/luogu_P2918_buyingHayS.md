---
title: "洛谷 P2918 [USACO08NOV] Buying Hay S"
date: 2023-12-12T16:44:32+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "完全背包"]
categories: ""
description: "这题并不是以花费的钱数为维度进行 DP 求解，而是以干草数量作为纬度进行求解，求出购买的干草数量严格等于目标值时获取的最小花费。"
---

> [洛谷 P2918 [USACO08NOV] Buying Hay S](https://www.luogu.com.cn/problem/P2918)
>
> [算法讲解074【必备】背包dp-分组背包、完全背包](https://www.bilibili.com/video/BV1UM411f7YL/)

### 思路

如果使用花费钱数作为 DP 维度，根据计算得知，花费的钱数最多为 $2.5 \times 10^8$，需要 125 MB的空间开辟一个 DP 表，显然是不行的。

如果使用干草数量作为 DP 维度，干草数量显然不会超出最大值很多，是可以开辟足够空间的。

### 求解过程

**最大的干草数量**

需要购买的干草数量最多为  $H(1 <= H <= 50000)$，没捆干草最多为 5000，所以可能当前购买的干草不够 H，但是加上下一捆干草数量就够了，也就不需要再买了，多了也不优惠。因此干草数量最多为 55000。 对于具体的实例，**最大的干草数量为 需要购买的干草数量加上干草最大捆的数量**。

**定义**
- `i` 为在前 `i` 家公司购买干草
- `j` 为购买的干草数量严格等于 `j`
- **`dp[i][j]` 为在前 `i` 家公司购买的干草数量严格等于 `j` 的最小花费**。

**base case**

- `dp[0][0] = 0`：在前 0 家选购干草数量为 0 的最小花费。
- `dp[0][1 -> m] = Integer.MAX_VALUE`: 在前 0 家选购干草数量为 m 的最小花费，因为没有公司，无法选购干草，设置为 MAX_VALUE 是表示方案不可达。

**DP过程**

当前来到第 i 家公司：

1. 如果一捆都不买：`dp[i][j] = dp[i - 1][j]`
2. 如果买一捆：`dp[i][j] = dp[i - 1][j - value[i]] + cost[i]`，因为上面的不买方案 `dp[i][j] = dp[i - 1][j]`，所以等价于 `dp[i][j] = dp[i][j - value[i]] + cost[i]`
3. 购买 n 捆：`dp[i][j] = dp[i][j - value[i]] + cost[i]`，计算之前已经在 i 家公司买了 n - 1 捆。

2 3 可以合并为一个表达式，在上述所有情况中选择最小值作为 dp[i][j] 的结果。

在 DP 过程中，需要先判断上一个方案是否可行，如果不可行，就不能继续购买。 最终，在达到购买要求的干草数量上遍历，取出花费最小的方案。

**空间压缩**

跟完全背包一样的压缩方式，分析依赖关系，确定遍历顺序...

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(m)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXH = 55001;
    public static int MAXN = 101;
    public static int[] cost = new int[MAXN];
    public static int[] value = new int[MAXN];
    public static int[] dp = new int[MAXH];
    public static int n, h, m;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval; in.nextToken();
            h = (int)in.nval;
            int maxV = 0;
            for (int i = 1; i <= n; i++) {
                in.nextToken(); value[i] = (int)in.nval;
                maxV = Math.max(maxV, value[i]);
                in.nextToken(); cost[i] = (int)in.nval;
            }
            m = h + maxV; // 最多购入这么多干草
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute() {
        Arrays.fill(dp, 1, m + 1, Integer.MAX_VALUE);
        for (int i = 1; i <= n; i++) {
            for (int j = value[i]; j <= m ; j++) {
                if (dp[j - value[i]] != Integer.MAX_VALUE) {
                    dp[j] = Math.min(dp[j], dp[j - value[i]] + cost[i]);
                }
            }
        }
        int ans = Integer.MAX_VALUE;
        for (int i = h; i <= m; i++) {
            ans = Math.min(ans, dp[i]);
        }
        return ans;
    }
}
```
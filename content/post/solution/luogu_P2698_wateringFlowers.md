---
title: "洛谷 P2698 [USACO12MAR] Flowerpot S"
date: 2023-12-09T21:34:27+08:00
draft: false
math: true

tags: ["洛谷", "排序", "单调队列"]
categories: ""
description: "将雨滴按照 x 坐标进行排序，然后使用单调队列记录下区间 y 坐标的最大值和最小值，如果差值不满足条件就一直右扩，满足条件后求出花盆长度，在所有花盆中取最小值。"
---

> [洛谷 P2698 [USACO12MAR] Flowerpot S](https://www.luogu.com.cn/problem/P2698)

### 思路

> 思路主要来自B站左程云的视频课程 [算法讲解054【必备】单调队列-上](https://www.bilibili.com/video/BV11h4y1w7Bu/)

求花盆的长度，满足下面的条件，在花盆区间内，第一滴雨水与最后一滴雨水落下的时间差要 `>= d`。雨水下落速度恒定 1 单位每秒，也就是求最大高度差满足 `>= d`。

使用单调队列，维护区间的最大值和最小值，如果满足条件就记录下答案，在所有答案中取最小值。

单调性分析：对于当前维护的区间：区间右扩，最大值可能会变得更大，最小值可能会变得更小，导致结果更符合要求；区间左边界缩小，最大值可能会变小，最小值可能会变大，导致越来越不符合要求。说明对于单调区间存在单调性，可以使用单调队列解决。

如何维护区间大小？必须使用数据来维护单调区间的左缩和右扩，时间差 d 就可以作为单调区间变化的条件，如果最大值最小值的差值不满足条件就可以右扩，如果差值满足条件了就可以停止了。如果是因为符合要求而停止的循环，更新结果，然后左边界右移，更新单调区间。

### 求解过程

因为雨水的顺序是乱序的，先进行排序，然后开始遍历，使用单调栈维护区间的最大值和最小值。

先判断当前区间是否符合要求，如果不符合要求，右边界向右扩充，一直扩充到符合要求（前提是没有到数组边界）。然后判断是否符合要求，如果符合就记录下答案。最后左边界缩小，更新单调区间的最大值和最小值。

如果一直遍历完整个数组都没有符合要求的花盆，返回 -1。

### 复杂度

- 时间复杂度：$O(n \times log_2n)$

排序的时间复杂度是 $O(n \times log_2n)$，单调队列求解的过程是 $O(n)$ 的，每个数字最多入队一次，出队一次。最终的时间复杂度就是 $O(n \times log_2n)$。

- 空间复杂度：$O(n)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    // 最多有 N 滴水
    public static int MAXN = 100005;
    public static int[][] water = new int[MAXN][2];
    public static int[] maxDeque = new int[MAXN];
    public static int[] minDeque = new int[MAXN];
    public static int maxHead, maxTail, minHead, minTail;
    public static int n, d;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval;
            in.nextToken();
            d = (int)in.nval;
            for (int i = 0; i < n; i++) {
                in.nextToken();
                water[i][0] = (int)in.nval;
                in.nextToken();
                water[i][1] = (int)in.nval;
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute() {
        Arrays.sort(water, 0, n, (w1, w2) -> w1[0] - w2[0]);
        maxHead = maxTail = minHead = minTail = 0;
        int ans = Integer.MAX_VALUE;
        for (int l = 0, r = 0; l < n; l++) {
            // 不满足条件就一直往右扩
            while (!satisfy() && r < n) {
                while (maxHead < maxTail && water[maxDeque[maxTail - 1]][1] <= water[r][1]) {
                    maxTail--;
                }
                maxDeque[maxTail++] = r;
                while (minHead < minTail && water[minDeque[minTail - 1]][1] >= water[r][1]) {
                    minTail--;
                }
                minDeque[minTail++] = r;
                r++;
            }
            // 如果是因为 满足条件跳出来的，更新结果
            if (satisfy()) {
                ans = Math.min(ans, water[r - 1][0] - water[l][0]);
            }
            // 从队头出队
            if (maxHead < maxTail && l == maxDeque[maxHead]) {
                maxHead++;
            }
            if (minHead < minTail && l == minDeque[minHead]) {
                minHead++;
            }
        }
        return ans == Integer.MAX_VALUE ? -1 : ans;
    }

    public static boolean satisfy() {
        int max = maxHead < maxTail ? water[maxDeque[maxHead]][1] : 0;
        int min = minHead < minTail ? water[minDeque[minHead]][1] : 0;
        return max - min >= d;
    }
}
```
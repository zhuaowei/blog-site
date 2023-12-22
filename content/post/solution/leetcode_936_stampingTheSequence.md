---
title: "LeetCode 936. 戳印序列"
date: 2023-12-21T19:53:03+08:00
draft: false
math: true

tags: ["LeetCode", "拓扑排序"]
categories: ""
description: "盖印的顺序可以想象为一个图，如果前后两次盖印的区间有重叠部分，后面的印可以修正前面错误的位置，如果重叠部分存在错误字符，则可以认为二者之间存在一条“前后盖印的关系”，求出拓扑排序的逆序即为盖印的顺序。"
---

> [LeetCode 936. 戳印序列](https://leetcode.cn/problems/stamping-the-sequence/)
>
> [算法讲解059【必备】建图、链式前向星、拓扑排序](https://www.bilibili.com/video/BV1rj411k7tS/)

### 思路

盖印的顺序可以想象为一个图，如果前后两次盖印的区间有重叠部分，后面的印可以修正前面错误的位置，如果重叠部分存在错误字符，则可以认为二者之间存在一条“前后盖印的关系”，求出拓扑排序的逆序即为盖印的顺序。

### 求解过程

盖印的先后可以认为存在一条边，但是并不是所有的前后盖印都存在边。盖印的区间必须有重叠部分，且重叠部分的字符必须是错的，这样，后面的印才能将先盖的覆盖掉。

初始时，我们认为字符串的每一个位置都有 m 条边（m 为印的长度），也就是说每一个覆盖此字符的印都被印错了，需要后面的印修正。

然后遍历印在每一个位置的印章结果，如果正确，说明该字符不需要当前印章修正，入度减一，如果存在入度为 0 的点，说明该印章的所有位置都是正确的，是最后印的。如果错误，说明需要后面的印章进行修正，先建立一条边，方便后面修正。

根据拓扑排序，后印的字符可以修正错误的字符，也就是入度为 0 的点可以修正其他点，所以将该点指向其他错误的点的入度减一，如果修正后的印也全部正确（入度为 0），那么它可以修正其他的点。

因为印章可能存在频繁修，所以对于已经修正的位置，记录下来，下次修正时直接跳过。入队的顺序即为拓扑排序的顺序，所以可以直接将队列作为结果数组。因为拓扑排序的顺序与盖印的顺序是相反的，所以需要将拓扑排序逆序输出。

### 复杂度

- 时间复杂度：$O(nm)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public int[] movesToStamp(String stamp, String target) {
        char[] s = stamp.toCharArray();
        char[] t = target.toCharArray();
        int m = s.length, n = t.length;
        int size = n - m + 1;
        ArrayList<ArrayList<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<Integer>());
        }
        int[] indegree = new int[size];
        int[] queue = new int[size];
        int h = 0, r = 0;
        Arrays.fill(indegree, m);
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < m; j++) {
                if (s[j] == t[i + j]) {
                    if (--indegree[i] == 0) {
                        queue[r++] = i;
                    }
                } else {
                    graph.get(i + j).add(i);
                }
            }
        }
        boolean[] vis = new boolean[n];
        while (h < r) {
            int node = queue[h++];
            for (int j = 0; j < m; j++) {
                if (!vis[node + j]) {
                    vis[node + j] = true;
                    for (int next : graph.get(node + j)) {
                        if (--indegree[next] == 0) {
                            queue[r++] = next;
                        }
                    }
                }
            }
        }
        if (h == size) {
            for (int i = 0, temp; i < size / 2; i++) {
                temp = queue[i];
                queue[i] = queue[size - i - 1];
                queue[size - i - 1] = temp;
            }
            return queue;
        } else {
            return new int[0];
        }
    }
}
```
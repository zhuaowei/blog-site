---
title: "洛谷 U107394 拓扑排序模板"
date: 2023-12-22T16:35:28+08:00
draft: false
math: true

tags: ["洛谷", "链式前向星", "拓扑排序", "堆排序"]
categories: ""
description: "在洛谷上必须使用链式前向星建图。因为要输出字典序最小的拓扑排序，所以需要将入度为 0 的点进行排序，这里使用小根堆的方式进行排序。"
---

> [洛谷 U107394 拓扑排序模板](https://www.luogu.com.cn/problem/U107394)
> 
> [算法讲解059【必备】建图、链式前向星、拓扑排序](https://www.bilibili.com/video/BV1rj411k7tS/)

### 思路

牛客网和 leetcode 可以使用邻接表法建图，而洛谷是专为比赛用的，所以必须使用链式前向星建图。题目要求获得字典序最小的拓扑排序，所以可以使用小根堆存储入度为 0 的节点，这样每次弹出的节点都是满足要求的节点中字典序最小的。

### 求解过程

1. 使用链式前向星建图，同时统计每个节点的入度。
2. 将入度为 0 的节点加入堆。
3. 依次从堆中弹出一个节点，直到堆为空。
4. 遍历弹出节点的每一条边，将目的节点的入度减一，如果减之后为 0，添加到堆。循环 3 4 步骤。

> 关于链式前向星建图：[LeetCode 210. 课程表 II](/article/leetcode-210.-课程表-ii/)
>
> 关于小根堆的添加和弹出，可以参考本文的代码和注释。

### 复杂度

- 时间复杂度：$O(n \times log_2n)$

每个节点都会进入堆一次，出堆一次。进出的时间复杂度都是 $log_2n$

- 空间复杂度：$O(n + m)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 200001;
    public static int MAXM = 200001;
    public static int[] head = new int[MAXN];
    //    public static int[] weight = new int[MAXN];
    public static int[] next = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int[] indegree = new int[MAXN];
    public static int[] ans = new int[MAXN];
    public static int[] heap = new int[MAXN];
    public static int cnt, h, t, n, m, heapSize;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval; in.nextToken();
            m = (int)in.nval;
            // 初始化
            init();
            for (int i = 1, u, v; i <= m; i++) {
                in.nextToken(); u = (int)in.nval;
                in.nextToken(); v = (int)in.nval;
                addEdge(u, v);
                indegree[v]++;
            }
            if (compute()) {
                for (int i = 0; i < n - 1; i++) {
                    out.print(ans[i] + " ");
                }
                out.println(ans[n - 1]);
            } else {
                out.println(-1);
            }
        }
        out.flush();
        out.close();
        br.close();
    }
    public static void init() {
        Arrays.fill(head, 0, n + 1, 0);
        Arrays.fill(indegree, 0, n + 1, 0);
        cnt = 1;
        heapSize = 0;
    }
    // 将 u -> v 这条边建出来
    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        // weight[cnt] = w // 权值
        head[u] = cnt++;
    }
    public static boolean compute() {
        // 返回是否能完成拓扑排序
        for (int i = 1; i <= n; i++) {
            if (indegree[i] == 0) {
                // 插入堆
                add(i);
            }
        }
        int index = 0;
        while (heapSize > 0) {
            int node = poll();
            ans[index++] = node;
            for (int e = head[node]; e != 0; e = next[e]) {
                if (--indegree[to[e]] == 0) {
                    add(to[e]);
                }
            }
        }
        return true;
    }
    // 小根堆
    public static void add(int val) {
        // 先添加到堆的末尾，然后逐渐向上层移动。
        heap[heapSize] = val;
        int index = heapSize; // 当前位置
        // 如果当前节点比父节点小，交换父节点和当前节点
        while (heap[index] < heap[(index - 1) / 2]) {
            // 交换
            swap(heap, index, (index - 1) / 2);
            index = (index - 1) / 2;
        }
        // 添加完毕后，堆的大小加一
        heapSize++;
    }
    public static int poll() {
        // 堆顶的值，为数组中的最小值，需要返回这个
        int value = heap[0];
        // 将堆顶的值交换到末尾，堆的大小减一
        swap(heap, 0, --heapSize);
        // 向下移动
        // 现在堆顶的值是一个比较大的值，需要将它向下移动
        int index = 0;
        int left = 1; // 左孩子
        while (left < heapSize) {
            // 当前节点和左孩子谁比较小
            int less = heap[left] < heap[index] ? left : index;
            // 较小值与右孩子谁更小（如果有）
            int lest = left + 1 < heapSize && heap[left + 1] < heap[less] ? left + 1 : less;
            // 如果三者最小值是自己，不需要向下移动了
            if (lest == index) {
                break;
            } else {
                // 否则与最小值交换，继续向下移动
                swap(heap, index, lest);
            }
            index = lest;
            left = 2 * index + 1;
        }
        return value;
    }
    // 交换数组中的两个值
    public static void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```
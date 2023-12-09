---
title: "洛谷 P8776 [蓝桥杯 2022 省 A] 最长不下降子序列"
date: 2023-12-09T16:58:10+08:00
draft: false
math: true

tags: ["洛谷", "DP", "二分", "最长递增子序列"]
categories: ""
description: "最长递增子序列问题扩展，需要将数组根据 k 分段，对左侧和右侧分别求最长非降序子序列长度，求最大值。"

links:
  - title: 左程云的个人主页 - 哔哩哔哩视频
    description: 本人号，详解各种算法和数据结构，代码和资料：https://github.com/algorithmzuo
    website: https://space.bilibili.com/8888480
    image: 
---

> [洛谷 P8776 [蓝桥杯 2022 省 A] 最长不下降子序列](https://www.luogu.com.cn/problem/P8776)

## 思路

> 思路来自 [B站左程云的视频课程](https://www.bilibili.com/video/BV1ne411D7CQ/)，在文章末尾可以直接转到左程云的B站个人空间。

求最长非降序子序列，在此基础上，给你一个机会，使长度为 k 的数组全部变成同一个数字，机会可以用，也可以不用，求此时的最长非降序子序列。

首先，使用这个机会可能会使结果变大，但是肯定不会使结果变小，所以选择使用这个机会。

然后是使用这个机会的时机，假如现在 `i..(k)..j` 可以形成非降序子序列，把 k 向后移动，使它紧贴在 j 的前面 `i....(k)j`，最终结果并不会改变，但是这样更容易分析了。我们需要求的是 k 之前的最长递增子序列，这些序列的结尾需要 `<= j`，还有 j 及之后的数组中，以 j 为开头的非降序子序列。最终结果就是 **左侧的长度 + k + 右侧的长度**。

## 求解过程

明确了要求什么，现在来看如何求。左侧的非降序子序列跟正常求非降序子序列一样。

右侧需要反向求，**以 j 为开头的非降序子序列 等价与 从后往前的以 j 为结尾的非升序子序列**，逆序求非升序子序列，跟左侧的非降序子序列差不多。因为右侧的数组长度不断变小，不好求，先求出每个 j 的以 j 为开头的非降序子序列长度，使用数组存储起来，使用的时候根据 j 直接取就可以了。

求出右侧的每个位置的长度之后，开始从头遍历，依次求出将 j 的前 k 个数变成 j 的最长非降序子序列长度，取最大值。

**特殊情况**

最后 k 个元素，变成数组中的最大值，因为右侧没有了 j ，他们可以变成任意大的数。此时最长非降序子序列就是前 n - k 长度的数组的最长非降序子序列加上 k。

## 复杂度

- 时间复杂度：$O(n \times log_2n)$

- 空间复杂度：$O(n)$

## 代码

```java
import java.io.*;

public class Main {
    public static int MAXN = 100005;
    public static int[] nums = new int[MAXN];
    public static int[] end = new int[MAXN];
    public static int[] right = new int[MAXN];
    public static int n;
    public static int k;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));

        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval;
            in.nextToken();
            k = (int)in.nval;
            for (int i = 0; i < n; i++) {
                in.nextToken();
                nums[i] = (int)in.nval;
            }
            if (k >= n) {
                out.println(n);
            } else {
                out.println(compute());
            }
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute() {
        // 先计算出每一个位置开头，后续的最长非递减子序列的最长长度
        generateRight();
        int len = 0,  ans = 0;
        for (int i = 0, j = k, index, L, R; j < n; i++, j++) {
            // 让 [i, j - 1] 这一段全部变成 nums[j]
            // 左侧 [0, i - 1] 找到找到 <= nums[j] 的最长非降序子序列
            // 右侧 [j, n - 1] 找到以 nums[j] 为开头的最长非降序子序列

            // 现将左侧数组计算出 end 数组
            index = findInLeftEnd(end, len, nums[j]);
            L = index == -1 ? len : index; // 左侧的最长非降序子序列（以 nums[j]） 结尾
            R = right[j];
            ans = Math.max(ans, L + k + R);
            // 更新 左侧 end 数组。因为下一次左侧边界要右移了
            index = findInLeftEnd(end, len, nums[i]);
            if (index == -1) {
                end[len++] = nums[i];
            } else {
                end[index] = nums[i];
            }
        }
        // 右侧没有了 j ，他们可以变成任意大的数
        ans = Math.max(ans, len + k);
        return ans;
    }
    // 在左侧 end 数组中找到 > target 的最长非递减子序列的长度
    public static int findInLeftEnd(int[] end, int len, int target) {
        int left = 0, right = len - 1, mid, ans = -1;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (end[mid] > target) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return  ans;
    }
    // 在右侧 end 数组中找到 < nums[j] 的最长非递增子序列的长度
    public static int findInRightEnd(int[] end, int len, int target) {
        int left = 0, right = len - 1, mid, ans = -1;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (end[mid] < target) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }

    // 从 nums 数组右往左生成 非递增子序列，求 每一个 j 位置的最长子序列长度
    public static void generateRight() {
        int len = 0;
        for (int i = n - 1, index; i >= 0; i--) {
            index = findInRightEnd(end, len, nums[i]);
            if (index == -1) {
                end[len++] = nums[i];
                right[i] = len;
            } else {
                end[index] = nums[i];
                right[i] = index + 1;
            }
        }
    }
}
```
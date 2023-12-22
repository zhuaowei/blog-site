---
title: "LeetCode 2276. 统计区间中的整数数目"
date: 2023-12-16T21:20:01+08:00
draft: false
math: true

tags: ["LeetCode", "线段树", "动态开点线段树", "每日一题"]
categories: ""
description: "2023-12-16 leetcode 每日一题。需要频繁将某个区间的值修改为指定值，频繁查询区间的值，很明显这道题可以使用线段树来求解。"
---

> [LeetCode 2276. 统计区间中的整数数目](https://leetcode.cn/problems/count-integers-in-intervals/)

### 思路

按题目要求，需要频繁将某个区间的值修改为指定值，频繁查询区间的值。很明显这道题可以使用线段树来求解。题解的最后是动态开点线段树的模板。

### 求解过程

1. 根据题目的范围开辟线段树，设置懒标记的初始值。
2. 实现区间更新和区间查询功能。

**线段树原理**

使用二叉树维护区间的和，二叉树的节点属性包括区间的左边界、右边界、中点、区间的值，还有懒标记，降低时间复杂度。

在进行区间更新和区间查询的时候 `[left, right]`，从二叉树的头节点开始往下遍历。以查询为例，根据当前节点 `[L, R]` 的区间与查询的区间进行对比，分为以下三种情况：

1. 如果两个区间没有交集 `R < left || L > right`，不是目标区间，返回 0，结束递归。
2. 如果查询区间包含节点区间 `leff <= L && R <= right`，查询时直接返回结果，更新时修改节点的值。
3. 如果两个区间有交集但不是包含的关系，就是除去上面两种情况。需要根据情况选择左节点还是右节点。

    a. 如果区间与节点左半部分有交集 `left <= mid`，调用左侧的节点进行递归。

    b. 如果区间与节点右半部分有交集 `mid < right`，调用右侧的节点进行递归。

    c. 最终将两种可能性的结果进行相加，返回。

**懒标记**

使用懒标记，在查询区间完全包含节点区间时，可以根据懒标记快速返回区间值。在更新时，可以暂时不进行底层节点的更新，等下次查询时再更新。使用懒标记可以优化时间复杂度。

懒标记向下传递的过程：

1. 检查懒标记是否为标记了本节点，如果没有，直接返回；
2. 如果有，更新当前节点的值，然后传递给左右子节点。
3. 最终消除本节点的懒标记。

### 复杂度

- 时间复杂度：$O(log_2n)$
- 空间复杂度：$(m \times log_2n)$

n 为区间的长度，m 为总查询次数。动态开点线段是是在添加和查询的过程中进行建树的，空间复杂度会比较低，如果是正常的则为 $O(n)$。

### 代码

```java
class CountIntervals {
    SegmentTree tree = new SegmentTree();
    public CountIntervals() {

    }
    
    public void add(int left, int right) {
        tree.update(left, right, 1);
    }
    
    public int count() {
        return tree.query();
    }
}

class Node {
    int left;
    int right;
    int mid;
    int lazy = 0;
    int val;
    Node leftNode, rightNode;

    Node() {
        this.lazy = 0;
    }
    Node(int l, int r) {
        this.left = l;
        this.right = r;
        this.mid = l + ((r - l) >> 1);
        this.lazy = 0;
    }
}

public class SegmentTree {
    public static int N = (int) 1e9;
    private Node root = new Node(1, N);

    SegmentTree() {
    }

    public void update(int left, int right, int val) {
        _update(root, left, right, val);
    }

    public int query() {
        return _query(root, 1, N);
    }

    public int query(int left, int right) {
        return _query(root, left, right);
    }

    private void _update(Node root, int left, int right, int val) {
        if (left <= root.left && root.right <= right) {
            root.lazy = val;
            root.val = (root.right - root.left + 1) * val;
            return;
        }
        if (root.left > right || root.right < left) {
            return;
        }
        _pushdown(root); // 在这里建树

        if (left <= root.mid) {
            _update(root.leftNode, left, right, val);
        }
        if (right > root.mid) {
            _update(root.rightNode, left, right, val);
        }
        _pushup(root);
    }

    private int _query(Node root, int left, int right) {
        if (left <= root.left && right >= root.right) {
            return root.val;
        }
        if (root.left > right || root.right < left) {
            return 0;
        }
        _pushdown(root);
        int res = 0;
        if (left <= root.mid) {
            res += _query(root.leftNode, left, right);
        }
        if (right > root.mid) {
            res += _query(root.rightNode, left, right);
        }
        return res;
    }

    private void _pushup(Node root) {
        if (root.leftNode != null && root.rightNode != null) {
            root.val = root.leftNode.val + root.rightNode.val;
        }
    }

    private void _pushdown(Node root) {
        if (root.leftNode == null) {
            root.leftNode = new Node(root.left, root.mid);
        }
        if (root.rightNode == null) {
            root.rightNode = new Node(root.mid + 1, root.right);
        }
        if (root.lazy != 0) {
            int temp = root.lazy;
            root.leftNode.lazy = temp;
            root.leftNode.val = (root.leftNode.right - root.leftNode.left + 1) * temp;
            root.rightNode.lazy = temp;
            root.rightNode.val = (root.rightNode.right - root.rightNode.left + 1) * temp;
            root.lazy = 0;
        }
    }
}
```
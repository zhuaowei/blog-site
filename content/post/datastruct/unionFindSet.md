---
title: "并查集以及感染算法计算岛的数量"
date: 2022-11-14T18:36:50+08:00
draft: false
math: false

tags: ["数据结构", "算法", "并查集"]
categories: ""
description: ""
---

## 1、并查集

并查集的结构设计就是都以比较快的速度实现这两个操作：合并两个集合、查询两个元素是否在同一个集合。

实现细节：

1、元素类：定义元素类，并定义一个构造函数，使并查集可以接收任意类型的集合。

2、并查集的属性：

    2.1、elementMap：根据元素原始类型可以查询到Element类型的map，存储所有加入并查集的元素。

    2.2、fatherMap：根据Element，可以查询到他的父元素。

    2.3、mapSize：记录头元素所代表的集合的大小。

> 所谓头元素，就是所有集合祖先元素，所有元素都可以通过不断向上查询找到它。

3、并查集的方法：

    3.1、isSameSet：给出两个元素，判断是否在同一个集合，返回一个布尔值。

    3.2、union：合并两个元素所在的集合，如果并查集中不存在其中元素或者两个元素已经在同一个集合了，就不做任何操作。

    3.3、findHead：给出一个元素，查找它的头元素（集合的代表元素）。同时，把路径上经过的元素的父元素都改成头元素，这个操作也叫扁平化处理，能够提高查询的速度。

4、并查集的初始化：

    4.1、初始化要一次性把所有元素都加进来，之后不能再添加元素。

    4.2、初始化时，所有元素都加入elementmap，且每个元素都是自己单独一个集合，mapsize为 1.

并查集的实现代码放在最后（Java实现）。

## 2、感染法求岛的个数

例题：有一个二位数组，只存储 0 和 1，0 代表海洋，1代表陆地。陆地可以跟上下左右四个方向的陆地相连，形成小岛，单独一块陆地也是小岛，求数组中小岛的数量。

解：使用感染法，两层 for 循环查询元素为 1 的值，对这个值调用感染函数，每次遇到 1，就将小岛数加 1。函数中使其值为 2，然后继续递归调用函数，感染上下左右四个陆地。停止条件为陆地超出边界或者 不为 1.

```java
// x, y 是陆地的座标，M, N 是数组的行数和列数。
public void infect(int[][] arr, int x, int y, int M, int N) {
    if (x < 0 || x >= N || y < 0 || y >= M || arr[x][y] != 1) {
        // 如果超过边界，感染过或者不是陆地，直接返回。
        return;
    }
    arr[x][y] = 2;
    // 分别是上下左右
    infect(arr, x, y - 1, M, N);
    infect(arr, x, y + 1, M, N);
    infect(arr, x - 1, y, M, N);
    infect(arr, x + 1, y, M, N);
}
```

因为数组是有边界的，而且感染的陆地被修改为2，不会让递归一直进行。时间复杂度为 `O（MN）`。

上面的是单线程的，因为所有的算法平台都是单核的，如果地图特别大，需要并行计算怎么办，如果只是简单地划分不同块，然后合并，得到的数字可能会变大，因为有岛会被切成多个。

解决办法是：使用并查集，在单独计算块内的岛时，特别记录一下位于边界的块，记录一下他们是从哪个块被感染的，存入并查集。合并时，将每一个块与相邻的块合并，如果原图是同一个岛，就将两个集合合并。

## 3、并查集实现代码

```java
public class UnionFindSet<V> {
    /**
     * 定义一个泛型类，给元素套上壳
     */
    public class Element<V> {
        public V value;

        public Element(V value) {
            this.value = value;
        }
    }

    // 加入并查集的元素，并查集只支持开始添加元素。
    public HashMap<V, Element<V>> elementMap;
    // 元素的父元素
    public HashMap<Element<V>, Element<V>> fatherMap;
    // 代表元素的 size，代表元素就是集合最上层的元素，只有这个元素才能有size属性
    public HashMap<Element<V>, Integer> mapSize;

    /**
     * 对元素列表进行并查集的初始化
     * 1. 所有元素加入elementmap
     * 2. 所有元素的父元素是自己
     * 3. 所有元素的size属性为1
     */
    public UnionFindSet (List<V> list) {
        elementMap = new HashMap<>();
        fatherMap = new HashMap<>();
        mapSize = new HashMap<>();

        for (V e: list) {
            elementMap.put(e, new Element<>(e));
            fatherMap.put(elementMap.get(e), elementMap.get(e));
            mapSize.put(elementMap.get(e), 1);
        }
    }

    public Element<V> findHead(Element<V> e) {
        Stack<Element<V>> stack = new Stack<>();
        // 一直向上找，直到找到某元素的父元素是自己，一路上将元素存入栈
        while (e != fatherMap.get(e)) {
            stack.push(e);
            e = fatherMap.get(e);
        }
        // 一次弹出栈，将元素的父元素设置为e。扁平化的目的是减少寻找的时间，降低复杂度。
        while (!stack.isEmpty()) {
            fatherMap.put(stack.pop(), e);
        }
        return e;
    }

    /**
     * 判断是否是在同一个集合
     */
    public boolean isSameSet(V a, V b) {
        if (elementMap.containsKey(a) && elementMap.containsKey(b)) {
            return findHead(elementMap.get(a)) == findHead(elementMap.get(b));
        }
        return false;
    }

    /**
     * 将 a 所在集合与 b 所在集合 合并
     */
    public void Union(V a, V b) {
        // 加入并查集才能合并
        if (elementMap.containsKey(a) && elementMap.containsKey(b)) {
            Element<V> aH = findHead(elementMap.get(a));
            Element<V> bH = findHead(elementMap.get(b));
            // 不是同一个头才合并
            if (aH != bH) {
                // 区分出哪个是大的，哪个是小的。小的连到大的后面
                Element<V> big = mapSize.get(aH) >= mapSize.get(bH) ? aH : bH;
                Element<V> small = aH == big ? bH : aH;
                fatherMap.put(small, big);
                mapSize.put(big, mapSize.get(small) + mapSize.get(big));
                mapSize.remove(small);
            }
        }
    }
}
```
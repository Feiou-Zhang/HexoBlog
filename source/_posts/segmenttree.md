---
title: 区间树
date: 2018-03-10 13:19:29
tags: [Segment Tree]
categories: [数据结构]
---

### 基本操作
1. Segment Tree 是一个 Full Binary Tree，每个节点子节点数量为 0 或 2.
2. 给定含 n 个元素的数组区间，对应的 Segmeng Tree 节点数量最多为 2n - 1.
3. 偶数长度区间会拆出两个偶数OR奇数长度区间；奇数长度区间会拆出一个偶数+一个奇数长度区间， 最终以长度为 1 的区间为叶节点。
- Build O(n)
- Update O(log n)
- Query O(log n)

#### 201. Segment Tree Build （lintcode）
```java
/**
 * 题意：已知一个 区间,返回一个segment tree
 * */
public class SegmentTreeBuild0201 {
    /** time O(n) space O(n) 方法：
     * 思路：注意，当start 等于 end的时候，可以直接返回root，
     * 而且，区级为奇数的时候，左子树应该多一个，
     * 优化：
     * */
    public SegmentTreeNode build(int start, int end) {
        if (start > end) {
            return null;
        }
        SegmentTreeNode root = new SegmentTreeNode(start, end);
        if (start == end) {
            return root;
        }
        int mid = start + (end - start) / 2;
        root.left = build(start, mid);
        root.right = build(mid + 1, end);
        return root;
    }
}
```
#### 439. Segment Tree Build II 

```java
/**
 * 题意：给一个数组，建一个区间树
 * */
public class SegmentTreeBuildII0439 {
    /** time O(n) space O(n) 方法：
     * 思路：如果是叶子节点，就直接，把当前index的值付给当前节点，然后返回，
     * 否则，递归建左右子树，然后当前root的最大值，就是左右子树2者最大值
     * 优化：
     * */
    public SegmentTreeNode build(int[] A) {
        return A == null ? null : build(A, 0, A.length - 1, 0);
    }
    private SegmentTreeNode build(int[] a, int start, int end, int max) {
        if (start > end) {
            return null;
        }
        SegmentTreeNode root = new SegmentTreeNode(start, end, max);
        if (start == end) {
            root.max = a[start];
            return root;
        }
        int mid = start + (end - start) / 2;
        root.left = build(a, start, mid, max);
        root.right = build(a, mid + 1, end, max);
        root.max = Math.max(root.left.max, root.right.max);
        return root;
    }
}
```
#### 203. Segment Tree Modify
```java
/**
 * 题意：给一颗区间树，要求把其中一个index改为另外一个值
 * */
public class SegmentTreeModify0203 {
    /** time O(logn) space O(logn) 方法：
     * 思路：注意一些空指针 和越界的问题
     * 如果找到坐标为目标index的叶子节点，那么修改它的max值为value，否则，递归，二分法去找这个节点
     * 然后不断跟新root的max
     * 优化：
     * */
    public void modify(SegmentTreeNode root, int index, int value) {
        if (root == null || index < root.start || index > root.end) {
            return;
        }
        if (root.start == root.end && root.start == index) {
            root.max = value;
        }
        int mid = root.start + (root.end - root.start) / 2;
        if (index <= mid) {
            modify(root.left, index, value);
        } else {
            modify(root.right, index, value);
        }
        if (root.left != null) {
            root.max = Math.max(root.left.max, root.right.max);
        }
    }
}

```
#### 202. Segment Tree Query 
```java
/**
 * 题意：给一个区间树的root，找到某区间的最大值
 * */
public class SegmentTreeQuery0202 {
    /** time O(logn) space O(logn) 方法：
     * 思路：query的时候，要分3中情况，
     * 1. target区间，完全覆盖（totally overlap)当前root的区间
     * 2. target区间，完全不重叠（non overlap)当前root的区间
     * 3. 如果前两种不符合，那一定就是 partial overlap这种情况，局部重叠
     * 如果完全覆盖，那么当前root的值（最大，最小，sum）就是target区间的值
     * 如果完全不重叠，就返回（min，max或者0之类的）
     * 如果局部重叠，那么就递归返回左右子树，然后最后 比较最后答案
     * 优化：
     * */
    public int query(SegmentTreeNode root, int start, int end) {
        if (start <= root.start && end >= root.end) {
            return root.max;
        }
        if (end < root.start || start > root.end) {
            return Integer.MIN_VALUE;
        }
        int left = query(root.left, start, end);
        int right = query(root.right, start, end);
        return Math.max(left, right);
    }
}
```
#### 247. Segment Tree Query II
```java

/**
 * 题意：给一个区间树的root，找到某区间的count和
 * */
public class SegmentTreeQueryII0247 {
    public int query(SegmentTreeNode root, int start, int end) {
        if (root == null) {
            return 0;
        }
        if (start <= root.start && end >= root.end) {
            return root.max;
        }
        if (end < root.start || start > root.end) {
            return 0;
        }
        int left = query(root.left, start, end);
        int right = query(root.right, start, end);
        return left + right;
    }
}
```
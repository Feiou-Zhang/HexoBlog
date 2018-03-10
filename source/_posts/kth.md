---
title: 第k系列
date: 2018-02-22 19:16:32
tags: [Kth]
categories: [Algorithm]
---

如果看到k的字眼，首先应该要想到用 优先队列来做，想想是否可以用tuple来做个组合加入到队列
然后再想想看看能不能优化

#### 215. Kth Largest Element in an Array

```java
/**
 * 无序数组中，找到第k个大的数*/
public class KthLargestElementinanArray0215 {
    /** time O(n) space O(n) 方法：优先队列 或者 diy快排
     * 思路：1. 维护一个k大小的最小堆，扫一遍数组 ，如果堆得size大于k了，就poll出最小的
     * 这样，当堆里只有k个元素的时候，堆顶的就是第k大的
     * 复杂度的话，时间是 nlogk，空间是k
     * 优化：如果可以改input数组的话，可以自己实现一个快排，这样，可以接近去O（n）时间
     * 当结束一整次swap 之后，如果 len - l == k 说明，l现在就在从右边数第k个index上
     * 直接返回，否则，就按照逻辑。循环左边或右边
     * */
    public int findKthLargest(int[] nums, int k) {
        if (nums == null) {
            return 0;
        }
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            int l = left;
            int r = right;
            int pivot = nums[l];
            while (l < r) {
                while (l < r && nums[r] >= pivot) {
                    --r;
                }
                swap(nums, r, l);
                while (l < r && nums[l] <= pivot) {
                    ++l;
                }
                swap(nums, l, r);
            }
            if (nums.length - l == k) {
                return nums[l];
            } else if (nums.length - l > k) {
                left = l + 1;
            } else {
                right = r - 1;
            }
        }
        return nums[left];
    }
    private void swap (int[] nums, int a, int b) {
        int tem = nums[a];
        nums[a] = nums[b];
        nums[b] = tem;
    }
}

```
#### 378. Kth Smallest Element in a Sorted Matrix

```java
/**
 * 题意：给一个n x n 矩阵，每排 每列都是排序好的，求第k个最小的数
 * */
public class KthSmallestElementinaSortedMatrix0378 {
    /** time O(klogk) k是number of rows, space O(k) 方法：优先队列
     * 思路：建一个tuple类，放value 和正纵坐标，先把第一列的元素都放进优先队列
     * 然后每次拿出最小的，然后加入拿出来的这个元素后面的一个元素。循环k-1次
     * 队首的元素就是第k小的
     * 优化：
     * */
    public int kthSmallest(int[][] matrix, int k) {
        Queue<Tuple> pq = new PriorityQueue<>(new Comparator<Tuple>() {
            @Override
            public int compare(Tuple o1, Tuple o2) {
                return o1.value - o2.value;
            }
        });
        for (int row = 0; row < matrix.length; ++row) {
            pq.offer(new Tuple(matrix[row][0], row, 0));
        }
        int col = matrix[0].length;
        for (int i = 1; i < k; ++i) {
            Tuple curr = pq.poll();
            if (curr.y + 1 < col) {
                pq.offer(new Tuple(matrix[curr.x][curr.y + 1], curr.x, curr.y + 1));
            }
        }
        return pq.poll().value;
    }
    class Tuple {
        int value;
        int x;
        int y;
        public Tuple(int value, int x, int y) {
            this.value = value;
            this.x = x;
            this.y = y;
        }
    }
}
```
#### 786. K-th Smallest Prime Fraction
```java
/**
 * 题意： 给一组prime， 以任意数为分子或分母，两两配对，求最小的第k个分数的分子和分母
 * Examples:
 Input: A = [1, 2, 3, 5], K = 3
 Output: [2, 5]
 Explanation:
 The fractions to be considered in sorted order are:
 1/5, 1/3, 2/5, 1/2, 3/5, 2/3.
 The third fraction is 2/5.
 * */
public class KthSmallestPrimeFraction0786 {
    /** time O(klogn) space O(k) 方法：优先队列
     * 思路：建一个tuple类，with3个参数，分子的index 分母的index，和分数
     * 把 数组里面的所有数的分之一都加入队列，然后，依次poll出最小的，然后把该tuple得分子index加一
     * 注意，这里加入分数的时候，要把分子 *1.0，要不然，可能加进去的都是0.0
     * 优化：
     * */
    public int[] kthSmallestPrimeFraction(int[] A, int K) {
        Queue<Tuple> pq = new PriorityQueue<>(new Comparator<Tuple>() {
            @Override
            public int compare(Tuple o1, Tuple o2) {
                if (o1.value > o2.value) {
                    return 1;
                } else if (o1.value < o2.value){
                    return -1;
                }
                return 0;
            }
        });
        for (int i = 0; i < A.length; ++i) {
            pq.offer(new Tuple(A[0] * 1.0 / A[i], 0, i));
        }
        for (int i = 1; i < K; ++i) {
            Tuple curr = pq.poll();
            if (curr.numerator + 1 < A.length) {
                double quotient = A[curr.numerator + 1] * 1.0  / A[curr.denominator];
                pq.offer(new Tuple(quotient, curr.numerator + 1, curr.denominator));
            }
        }
        Tuple kthSmall = pq.poll();
        return new int[]{A[kthSmall.numerator], A[kthSmall.denominator]};
    }
    class Tuple {
        double value;
        int numerator;
        int denominator;
        Tuple (double value, int numerator, int denominator) {
            this.value = value;
            this.numerator = numerator;
            this.denominator = denominator;
        }
    }
}

```
#### 347. Top K Frequent Elements
```java
/**
 * 题意：给一个非0数组，返回第k个最频繁出现的数字
 * */
public class TopKFrequentElements0347 {
    /** time O(nlogk) space O(n) 方法：hashmap + 优先队列
     * 思路：用一个map统计一下词频，然后维护一个k大小的优先队列，
     * 把所有词频组合加入队列，只保留k个，最后遍历队列，加入结果
     * 优化：
     * */
    public List<Integer> topKFrequent(int[] nums, int k) {
        List<Integer> res = new ArrayList<>(k);
        if (nums == null || nums.length == 0) {
            return res;
        }
        int n = nums.length;
        Map<Integer, Integer> map = new HashMap<>(n);
        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        /**
         PriorityQueue<Map.Entry<Integer, Integer>> miniHeap =
         new PriorityQueue<>((a, b)->(a.getValue() - b.getValue()));
         */
        Queue<Tuple> pq = new PriorityQueue<>(new Comparator<Tuple>() {
            @Override
            public int compare(Tuple o1, Tuple o2) {
                return o1.count - o2.count;
            }
        });
        for (Map.Entry<Integer, Integer> item : map.entrySet()) {
            pq.offer(new Tuple(item.getKey(), item.getValue()));
            if (pq.size() > k) {
                pq.poll();
            }
        }
        while (!pq.isEmpty()) {
            res.add(pq.poll().data);
        }
        return res;
    }
    class Tuple {
        int data;
        int count;
        Tuple (int data, int count) {
            this.data = data;
            this.count = count;
        }
    }
}

```

#### 230. Kth Smallest Element in a BST

```java
/**
 * 题目大意： 找到在bst里面第k小的元素
 Note:
 You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

 Follow up:
 如何优化 What if the BST is modified (insert/delete operations) often
 and you need to find the kth smallest frequently?
 * */
public class KthSmallestElementinaBST0230 {
    /** time O(k) space O(1) 方法：
     * 思路：中序遍历，遍历到在第k个数停止。这个题用二分也可以做，但二分的复杂度反而更高
     * 因为需要每次看当前数一共有多少节点。但其实，如果建树的时候，加入这个属性，就可以用O(H)
     * 优化：
     * follow up： 这题的难点其实在于Follow Up：如果我们频繁的操作该树，并且频繁的调用kth函数，
     * 有什么优化方法使时间复杂度降低至O(h)？h是树的高度。我们可以在TreeNode中加入一个rank成员
     * 这个变量记录的是该节点的左子树中节点的个数，其实就是有多少个节点比该节点小。
     * 这样我们就可以用二叉树搜索的方法来解决。这个添加rank的操作可以在建树的时候一起完成。
     * https://segmentfault.com/a/1190000008305885
     * */
    public int kthSmallest(TreeNode root, int k) {
        TreeNode head = root;
        while (head != null) {
            if (head.left == null) {
                if (--k == 0) {
                    return head.val;
                }
                head = head.right;
            } else {
                TreeNode tmp = head.left;
                while (tmp.right != null && tmp.right != head) {
                    tmp = tmp.right;
                }
                if (tmp.right == null) {
                    tmp.right = head;
                    head = head.left;
                } else {
                    if (--k == 0) {
                        return head.val;
                    }
                    head = head.right;
                    tmp.right = null;
                }
            }

        }
        return -1;
    }
}

```
---
title: 子序列
date: 2018-03-30 12:24:48
tags: [Subsequence]
categories: [Algorithm]
---
#### 334. Increasing Triplet Subsequence

```java
/**
 * 题意：看一个无序数组里面是否有一个升序序列 包含大于等于3个元素
 * */
public class IncreasingTripletSubsequence0334 {
    /** time O(n) space O(1) 方法：
     * 思路：维护2个变量，来更新2个最小的值，
     * 如果碰到一个比第二小的值 更大的值，就返回true
     * 如果碰到比最小值还小的，那么就更新最小值， ** 这里需要注意，并不需要重置次小值
     * 因为在这个新的最小值前面，已经有有一组， 最小 次小，， 所以次小值 仍然有效
     * 如果碰到比最小值大，但比第二小值小的，更新第二小的值，
     * 优化：
     * */
    public boolean increasingTriplet(int[] nums) {
        if (nums == null || nums.length < 3) {
            return false;
        }
        int smallest = Integer.MAX_VALUE;
        int smaller = Integer.MAX_VALUE;

        for (int num : nums) {
            if (num > smaller) {
                return true;
            }
            if (num < smallest) {
                smallest = num;
                //smaller = Integer.MAX_VALUE; //错误
            }
            if (num > smallest && num < smaller) {
                smaller = num;
            }
        }
        return false;
    }
}
```

### 二分法

#### 300. Longest Increasing Subsequence
```java

/**
 * 题意：给一个无序数组，找到最长的升序子序列的长度
 * 比如 [10, 9, 2, 5, 3, 7, 101, 18], 最长的升序子序列是 [2, 3, 7, 101], 长度是 4.
 * */
public class LongestIncreasingSubsequence0300 {
    /** time O(nlogn) space O(n) 方法：二分查找
     * 思路：用一个数组来记一下，每一个长度的子序列的最大的值，然后不断更新这个数组来找到结果
     * 具体更新的方法 要分3中情况来处理
     * 1. 如果当前数字，小于 tails数组的第一个元素，那么tails[0] = nums[i]
     * 2. 如果当前数字 大于 tails的最后一个元素，就把tails的长度 增加一位，并赋值
     * 3. 如果当前数字 处于tails 的中间，那么就用二分查找。去替换那个刚好比当前数字大一点的元素
     * 注意，如果当前数字正好和tails中的某一个数 一样，那么就跳过。
     * 优化：这题也可以用dp做，但反而时间复杂度更高 为 n^2
     * */
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int[] tails = new int[nums.length];
        tails[0] = nums[0];
        int len = 1;
        for (int i = 1; i < nums.length; ++i) {
            if (nums[i] < tails[0]) {
                tails[0] = nums[i];
            } else if (nums[i] > tails[len - 1]) {
                tails[len++] = nums[i];
            } else {
                binarySearch(nums[i], tails, len - 1);
            }
        }
        return len;
    }
    private void binarySearch(int num, int[] tails, int right) {
        int left = 0;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (tails[mid] == num) {
                return;
            }
            if (tails[mid] < num) {
                left = mid;
            } else {
                right = mid;
            }
        }
        if (num <= tails[left]) {
            tails[left] = num;
        } else {
            tails[right] = num;
        }
    }
}

```
#### 354. Russian Doll Envelopes
```java
/**
 * 题意：给一组俄罗斯套娃，二维矩阵，每行代表一个套娃，第一列代表宽度，第二轮代表高度
 * 求最多可以有多个套娃，可以套
 * */
public class RussianDollEnvelopes0354 {
    /** time O(nlogn) space O(n) 方法：二分法
     * 思路：可以先将所有套娃，按照宽 或 高 按升序排序，比如说 按 宽度 排序，
     * 那么如果这些套娃是按宽度升序排列好的，这个问题就可以转换成了 在高度这组数据里面
     * 找到一个最长的上升子序列，思考到了这里 ，问题就解决了一大半，
     * 还有一个小问题，需要解决的就是，如果2个套娃的宽度，相等 怎么办，
     * 比如 ，[2,4], [2,6] 如果宽度相等，高度是按升序排列的话，结果就错了 ，因为4，6是上升序列
     * 但 [2,4] 并不能放进 [2,6] 里面，分析到了这里，问题也就好解决了，那么就当宽相等的时候，高降序排列
     * 找无序数组的最长上升子序列就用leetcode300的做法就可以
     * 优化：
     * */
    public int maxEnvelopes(int[][] envelopes) {
        if (envelopes == null || envelopes.length == 0 || envelopes[0] == null ||
                envelopes[0].length == 0 ) {
            return 0;
        }
        Arrays.sort(envelopes, (a, b) -> (a[0] == b[0] ? b[1] - a[1] : a[0] - b[0]));
        int[] tails = new int[envelopes.length];
        tails[0] = envelopes[0][1];
        int len = 1;
        for (int i = 1; i < envelopes.length; ++i) {
            if (envelopes[i][1] < tails[0]) {
                tails[0] = envelopes[i][1];
            } else if (envelopes[i][1] > tails[len - 1]) {
                tails[len++] = envelopes[i][1];
            } else {
                binarySearch(tails, envelopes[i][1], 0, len - 1);
            }
        }
        return len;
    }
    private void binarySearch(int[] tails, int num, int left, int right) {
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (tails[mid] == num) {
                return;
            } else if (tails[mid] > num) {
                right = mid;
            } else {
                left = mid;
            }
        }
        if (num <= tails[left]) {
            tails[left] = num;
        } else {
            tails[right] = num;
        }
    }
}

```
---
title: 贪心
date: 2018-04-01 17:36:09
tags: [Greedy]
categories: [Algorithm]
---

#### 55. Jump Game
```java
/**
 * 题意：给一个数组，每个元素代表可以走到的最远距离，验证是否可以走到终点
 * */
public class JumpGame0055 {
    /** time O(n) space O(1) 方法：贪心
     * 思路：不断更新可以到达的最远距离
     * 优化：
     * */
    public boolean canJump(int[] nums) {
        //edge case
        if (nums == null || nums.length == 0) {
            return false;
        }
        int reach = nums[0];
        for (int i = 0; i <= reach; ++i) {
            reach = Math.max(reach, nums[i] + i);
            if (reach >= nums.length - 1) {
                return true;
            }
        }
        return false;
    }
}
```
#### 45. Jump Game II
```java

/**
 * 题意：给一个数组，每个元素代表可以走到的最远距离，求最少几步可以走到终点 
 * */
public class JumpGameII0045 {
    /** time O(n) space O(1) 方法：贪心
     * 思路：遍历数组，记录一个当前step能走到的最远的值，和下一步能走到的最远的值
     * 每次循环，先检查当前的 currmax是否能到达终点，如果是 ，直接返回步数
     * 然后更新一下 全局的最远距离，并查看 当前的i是不是到currmax， 如果是，++步数
     * 并更新currmax 为 全局最远距离
     * 优化：
     * */
    public int jump(int[] nums) {
        if (nums == null || nums.length < 2 || nums[0] == 0) {
            return 0;
        }
        int currMax = nums[0];
        int max = nums[0];
        int minSteps = 1;
        for (int i = 1; i < nums.length; ++i) {
            if (currMax >= nums.length - 1) {
                return minSteps;
            }
            max = Math.max(max, nums[i] + i);
            if (currMax == i) {
                ++minSteps;
                currMax = max;
            }
        }
        return minSteps;
    }
}
```

#### 435. Non-overlapping Intervals
```java
/**
 * 题意：给一些区间，找到最少删除几个区间可以使剩下的区间都不重叠
 * */
public class NonoverlappingIntervals0435 {
    /** time O(nlogn) space O(1) 方法：贪心
     * 思路：其实这个题 就是变相的在问，这些区间里面最多有多少个不重叠的区间，
     * 也就是最经典的 贪心问题之一的 活动安排问题，
     * 按照结束时间排序，策略就是每次选择 结束更早的那个区间，这样就给了更多余地选择后面的活动
     * 优化：
     * */
    public int eraseOverlapIntervals(Interval[] intervals) {
        if (intervals == null || intervals.length < 2) {
            return 0;
        }
        Arrays.sort(intervals, (a, b) -> a.end - b.end);
        int end = intervals[0].end;
        int count = 1;
        for (int i = 1; i < intervals.length; ++i) {
            if (intervals[i].start >= end) {
                ++count;
                end = intervals[i].end;
            }
        }
        return intervals.length - count;
    }
}
```
#### 621. Task Scheduler
```java
/**
 * 题意：给定一些任务，由大写字母[A-Z]表示，相同的任务之间需要隔n个其他任务或者idle
 * 求最少需要多少个单位的时间运行所有任务
 * */
public class TaskScheduler0621 {
    /** time O(n) space O(1) 方法：贪心
     * 思路：首先可以找到频率最多的任务的次数 m，首先可以确定，至少是需要 总任务个数 个单位时间的
     * 比如频率最多的是A，一共出现4次，n等于2，那么最后打印任务肯定会是像这样 AXX AXX AXX A
     * 最后一个A后面可能有东西，也可能没东西，depends 其余的任务是否可以全部装进所有XX的位置
     * 那么怎么判断 哪些可以放进XX的位置，哪些不能呢，
     * 其实也没有很复杂，如果某个任务的频率小于m，那么一定可以放进去，反之，如果某个任务的频率等于m
     * 那么也就一定放不进去，因为它(们)会和A并排占用X的位置
     * 经过这么分析，答案也就显而易见了，也就是 (m-1) * (n+1) + 词频为m的任务的个数
     * 优化：
     * */
    public int leastInterval(char[] tasks, int n) {
        int[] count = new int[26];
        for (char t : tasks) {
            ++count[t - 'A'];
        }
        int mostFrequent = 0;
        int cntMost = 0;
        for (int c : count) {
            if (c > mostFrequent) {
                mostFrequent = c;
                cntMost = 0;
            }
            if (c == mostFrequent) {
                ++cntMost;
            }
        }
        return Math.max(tasks.length, (mostFrequent - 1) * (n + 1) + cntMost);
    }
}

```
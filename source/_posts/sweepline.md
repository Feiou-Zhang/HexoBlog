---
title: 扫描线
date: 2018-03-28 13:22:00
tags: [Sweep Line]
categories: [Algorithm]
---
### meeting room

#### 252. Meeting Rooms
```java
/**
 * 题意：给一组会议的开始时间和结束时间，求一个人是否能参加所有会议
 * */
public class MeetingRooms0252 {
    /** time O(nlogn) space O(n) 方法：
     * 思路：分别排序开始时间和结束时间，然后看是否有会议的结束时间在任何会议的开始时间之前的
     * 优化：小优化就是用优先队列来做，但复杂度只会小提升，不会在量级上提升
     * */
    public boolean canAttendMeetings(Interval[] intervals) {
        if (intervals == null || intervals.length < 2) {
            return true;
        }
        int n = intervals.length;
        int[] start = new int[n];
        int[] end = new int[n];
        for (int i = 0; i < n; ++i) {
            start[i] = intervals[i].start;
            end[i] = intervals[i].end;
        }
        Arrays.sort(start);
        Arrays.sort(end);
        for (int i = 1; i < n; ++i) {
            if (end[i - 1] > start[i]) {
                return false;
            }
        }
        return true;
    }
}
```

#### 253. Meeting Rooms II
```java
/**
 * 题意：求最少需要几个meeting room 才能顺利安排所有的meeting
 * */
public class MeetingRoomsII0253 {
    /** time O(nlogn) space O(n) 方法：扫描线
     * 思路：还是先分别排序开始时间和结束时间，如果当前开始时间，在当前结束时间之前，
     * 那么就要增加一个房间
     * 如果当前开始时间在当前结束时间之后，就相当于，我们可以重复使用之前一个释放了的房间，
     * 这时候，只需要更新到下一个结束时间就可以
     * 优化：
     * */
    public int minMeetingRooms(Interval[] intervals) {
        if (intervals == null) {
            return 0;
        }
        int n = intervals.length;
        if (n < 2) {
            return n;
        }
        int rooms = 1;
        int[] start = new int[n];
        int[] end = new int[n];
        for (int i = 0; i < n; ++i) {
            start[i] = intervals[i].start;
            end[i] = intervals[i].end;
        }
        Arrays.sort(start);
        Arrays.sort(end);
        for (int s = 1, e = 0; s < n; ++s) {
            if (start[s] < end[e]) {
                ++rooms;
            } else {
                ++e;
            }
        }
        return rooms;
    }
}
```

#### 56. Merge Intervals
```java
/**
 * 题意：给一组区间，merge重叠部分，返回一组新的non overlap的区间，For example,
 Given [1,3],[2,6],[8,10],[15,18],
 return [1,6],[8,10],[15,18].
 * */
public class MergeIntervals0056 {
    /** time O(nlogn) space O(n) 方法：扫描线
     * 思路：还是先分别排序起点和终点，然后如果当前起点，在上一个终点之后，那么就把当前区间加入结果
     * 然后更新当前起点 为 下一个左边界，
     * 如果当前起点在上一个终点上，或之前，那么就跳过这个起点和终点，也就是什么都不用做
     * 循环结束后，要把最后一部分加入到结果。
     * 优化：
     * */
    public List<Interval> merge(List<Interval> intervals) {
        List<Interval> res = new ArrayList<>();
        if (intervals == null) {
            return res;
        }
        int n = intervals.size();
        if (n < 2) {
            return intervals;
        }
        int[] start = new int[n];
        int[] end = new int[n];
        for (int i = 0; i < n; ++i) {
            start[i] = intervals.get(i).start;
            end[i] = intervals.get(i).end;
        }
        Arrays.sort(start);
        Arrays.sort(end);
        int left = start[0];
        for (int s = 1, e = 0; s < n; ++s, ++e) {
            if (start[s] > end[e]) {
                res.add(new Interval(left, end[e]));
                left = start[s];
            }
        }
        res.add(new Interval(left, end[n - 1]));
        return res;
    }
}
```

#### 57. Insert Interval
```java
/**
 * 题意：给一组不重叠的区间，要求把一个区间加入到这个组里面，并且在需要的情况下 merge
 * */
public class InsertInterval0057 {
    /** time O(n) space O(n) 方法：扫描线
     * 思路：比较笨的办法就是加入进去，然后merge，但肯定就做了很多无用功
     * 正确的方法应该就是扫描list，这里会有几种情况
     * 1. 完全没重叠，或者 已经merge完成了，这时候，只需要把当前区间加到结果就可以了
     * 2. 找左边界： 当没找到左边界的情况下，如果新区间的起点，小于等于 当前区间的终点的时候，
     * 此时，新区间起点可能小于或大于当前区间的起点，所以要取二者更小的作为left，同时要标记找到了left
     * 3. 找右边界： 新区间的终点小于当前区间的起点，这时候，取上个区间的终点和新区间的终点 二者更大的
     * 4。如果再循环中都没有成功把新区间加入到结果中，说明，没找到右边界，就需要特殊处理下。
     * 优化：
     * */
    public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
        if (newInterval == null) {
            return intervals;
        }
        List<Interval> res = new ArrayList<>();
        if (intervals.isEmpty()) {
            res.add(newInterval);
            return res;
        }
        int n = intervals.size();
        int left = newInterval.start;
        int lastRight = newInterval.end;
        boolean added = false;
        boolean findLeft = false;
        for (Interval interval : intervals) {
            if (added || interval.end < newInterval.start) {
                res.add(interval);
                continue;
            }
            if (!findLeft && newInterval.start <= interval.end) {
                left = Math.min(interval.start, newInterval.start);
                findLeft = true;
            }
            if (newInterval.end < interval.start) {
                res.add(new Interval(left, Math.max(lastRight, newInterval.end)));
                res.add(interval);
                added = true;
            }
            lastRight = interval.end;
        }
        if (!added) {
            res.add(new Interval(left, Math.max(intervals.get(n - 1).end, newInterval.end)));
        }
        return res;
    }
    /** time O(n) space O(n) 方法：扫描线
     * 思路：分3种情况
     * 1. 当前区间的终点 小于 新区间的起点的时候，表示没有重叠情况，可以把当前区间加入结果
     * 如果这时候，全部区间都加入到结果中了，那么直接把新区间加到后面 返回既可
     * 2. 到这里，说明新区间的起点 大于或等于了 当前区间的起点，那么先 初始 target起点(二者取小）
     * 初始终点为 新区间的终点，然后，在 当前区间的起点，小于或等于 新区间的终点的时候，更新target终点
     * 如果循环结束，说明当前区间的起点 大于了 新区间的终点，那么就加入这个新区间到结果，overlap 结束
     * 3.如果后面还有区间，就直接加到结果中
     * 优化：
     * */
    public List<Interval> insert2(List<Interval> intervals, Interval newInterval) {
        if (newInterval == null) {
            return intervals;
        }
        List<Interval> res = new ArrayList<>();
        if (intervals == null || intervals.size() == 0) {
            res.add(newInterval);
            return res;
        }
        int i = 0;
        int n = intervals.size();
        for (; i < n && intervals.get(i).end < newInterval.start; ++i) {
            res.add(intervals.get(i));
        }
        if (i == n) {
            res.add(newInterval);
            return res;
        }
        int start = Math.min(intervals.get(i).start, newInterval.start);
        int end   = newInterval.end;
        for (; i < n && intervals.get(i).start <= newInterval.end; ++i) {
            end = Math.max(intervals.get(i).end, end);
        }
        res.add(new Interval(start, end));
        for (; i < n; ++i) {
            res.add(intervals.get(i));
        }
        return res;
    }
}

```
#### 163. Missing Ranges
```java
/**
 * 题意：给一个有序可能有重的数组，和一组上下边界，求missing的range 是什么
 * 例如： [0, 1, 3, 50, 75], lower = 0 and upper = 99,
 * return ["2", "4->49", "51->74", "76->99"].
 * */
public class MissingRanges0163 {
    /** time O(n) space O(1) 方法：
     * 思路：实现题，需要注意  各种边界条件, 和有重复元素的情况
     * 优化：
     * */
    public List<String> findMissingRanges(int[] nums, int lower, int upper) {
        List<String> res = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            addToResult(res, nums, lower, upper);
            return res;
        }
        if (lower < nums[0]) {
            addToResult(res, nums, lower, nums[0] - 1);
        }
        for (int i = 1; i < nums.length; ++i) {
            if (nums[i] != nums[i - 1] && nums[i] != nums[i - 1] + 1) {
                addToResult(res, nums, nums[i - 1] + 1, nums[i] - 1);
            }
        }
        if (upper > nums[nums.length - 1]) {
            addToResult(res, nums, nums[nums.length - 1] + 1, upper);
        }
        return res;
    }
    private void addToResult(List<String> res, int[] nums, int lower, int upper) {
        if (lower == upper) {
            res.add(String.valueOf(lower));
        } else {
            res.add(lower + "->" + upper);
        }
    }
}
```
#### 218. The Skyline Problem

```java
/**
 * 题意：给一组房子的数组，3个值组成，分别是起点，终点和高度，要求返回这些房子的轮廓线
 * 轮廓线就是所有最高点最左边的点。除了最低点之外
 * */
public class TheSkylineProblem0218 {
    /** time O(n^2) space O(n) 方法：扫描线
     * 思路：同一个高度，最左边的第一个点，肯定是一个key point，然后当 当前最高点点结束的时候，
     * 次高点也是 一个key point，所以，可以先把开始点，和结束点区分开，按照坐标升序排列
     * 然后既然我们只关心最高的高度， 那么也很容易想到可以专门用一个优先队列来储存高度，先加入一个0，以便对比
     * 遍历所有的点，如果当前点是楼的开始点，那么先看它的高度是否高于 队首高度，如果是，那么加入到结果
     * 然后不管是或不是，都把当前高度加入队列
     * 如果当前点是楼的结束点，那么先把相应的开始点从队列中删除，然后再看删除的高度是否是当前最高高度
     * 如果是，把次高点加入结果
     * 注意：这里还要注意一些特殊的情况，就是楼的重叠，和紧挨
     * 开始用了一个tuple类，里面用一个布尔变量来标记当前x坐标是开始点，还是结束点
     * 后来，遇到了重叠 和紧挨的问题后，发现调整起来特别麻烦，参考了下论坛的解法，确实把起始点的高度标为负 最好
     * **重要：紧挨的情况，需要先处理同一个x轴上的起点，然后再处理终点，这样就可以跳过多余的终点
     * 重叠的情况又分2种，起点重叠 和终点重叠, 如果是起点重合，那么我们需要从高到低处理，终点重叠，相反
     * 根据这些特殊情况，如果把起点的高度标记成负的话，就特别好处理了。
     * 优化：优先队列的删除比较耗时
     * */
    public List<int[]> getSkyline(int[][] buildings) {
        List<int[]> res = new ArrayList<>();
        if (buildings == null || buildings.length == 0 || buildings[0] == null || buildings[0].length == 0) {
            return res;
        }
        List<int[]> points = new ArrayList<>();
        for (int[] building : buildings) {
            points.add(new int[] {building[0], -building[2]});
            points.add(new int[] {building[1], building[2]});
        }
        points.sort(new Comparator<int[]>() {
            @Override
            public int compare(int[] a, int[] b) {
                return a[0] == b[0] ? a[1] - b[1] : a[0] - b[0];
            }
        });
        //points.sort((a, b) -> a[0] == b[0] ? a[1] - b[1] : a[0] - b[0]);
        Queue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        pq.offer(0);
        for (int[] point : points) {
            if (point[1] < 0) {
                if (-point[1] > pq.peek()) {
                    res.add(new int[] {point[0], -point[1]});
                }
                pq.offer(-point[1]);
            } else {
                pq.remove(point[1]);
                if (point[1] > pq.peek()) {
                    res.add(new int[] {point[0], pq.peek()});
                }
            }
        }
        return res;
    }
}

```
---
title: 杂题
date: 2018-02-22 02:37:15
tags: [Others]
categories: [Algorithm]
---

不懂 为什么是贪心的一个题


#### 649. Dota2 Senate
```java
/**
 * 题意，给一个字符串，里面有RD两种字母，每个代表一个党，每人有一个投票权
 * 而且排在前面的党可以否定后面一个敌对党的投票，被反对的人就移除队伍
 * 当遍历到最后一个字符的时候，如果剩余的队伍还没统一意见的话，那么继续收尾相连
 * 继续投票
 * */
public class Dota2Senate0649 {
    /** time O(n) space O(n) 方法：
     * 思路：用2个队列，里面分别放入2个党每个人的index，然后每次poll2个队首，index小的舍弃
     * index大的，加上 字符串的长度，继续放回队列，等待下一轮的投票
     * 优化：
     * */
    public String predictPartyVictory(String senate) {
        if (senate == null || senate.length() == 0) {
            return "";
        }
        Deque<Integer> r = new ArrayDeque<>();
        Deque<Integer> d = new ArrayDeque<>();
        for (int i = 0; i < senate.length(); ++i) {
            if (senate.charAt(i) == 'R') {
                r.offer(i);
            } else {
                d.offer(i);
            }
        }
        while (!r.isEmpty() && !d.isEmpty()) {
            int r_head = r.poll();
            int d_head = d.poll();
            if (r_head < d_head) {
                r.offer(r_head + senate.length());
            } else {
                d.offer(d_head + senate.length());
            }
        }
        return r.isEmpty() ? "Dire" : "Radiant";
    }
}
```
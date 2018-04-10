---
title: 杂题
date: 2018-02-22 02:37:15
tags: [Others]
categories: [Algorithm]
---

### KMP
Text: aabaaabaaac
Pattern: aabaaac

substrings of pattern
a
aa
aab
aaba
aabaa
aabaaa
aabaaac

KMP的整体思路就是，减少没有必要的重复比较，比较的时候，text的指针不用回头。
pattern的指针每次mis-match的时候，只回到上一个最好的位置重新比较

KMP算法有3个基本步骤

1. 构建next数组
indx: 0 1 2 3 4 5 6
text: a a b a a a c
next: 0 1 0 1 2 2 0

思路：

找到所有子串的公共前缀和后缀，之后，在做比较的时候，如果发现了mis-match的情况，
我们就可以很快的找到下一个最好的开始位置去比较，从而避免一些重复的工作。
构建next数组的时候，用j去扫描数组，用i去找公共的前缀和后缀
注意： 初始的时候，i为0， j为1


具体步骤：

    1. 当char i 和 char j  不同的时候，set(move) i to next[i - 1]，这里i必须大于0
    2. 如果char i 和 char j 相同，那么set next[j] to i + 1 and increment i by 1.

    比如： 当i = 0, j = 3的时候，我们找到了公共的前缀和后缀，这时候，我们需要做的就是看i后面的字符和j后面的字符一样不一样，是否可以组成更长的公告前后缀，
    更重要的事，当char i 和char j开始不一样的时候，比如当i = 2， j = 5的时候，
    这时候，如果简单的重置i就错了，因为i前面还有可能有部分子串满足，
    所以要把i移动到next[i - 1]的位置
2. 比较

比较的时候，我们用i指向text字符串，j指向pattern字符串，同时，j也指向next数组
这时候，我们已经构建好了next数组，我们知道了当mis-match的时候，我们应该把j 移动到next[j - 1]的位置重新比较，直到他们重新开始match或者j回到了0

如果char i 和 char j 一样的时候，那么就increment j，就可以了，直到j等于pattern的长度，就找到了子串

### GCD & LCM

Greatest Common Divisor 也就是 Euclidean algorithm 代码很简单，关键是要理解原理

比如找1112 和 695的最大公倍数，首先我们假设n是这个GCD，那么1112就等于 an， 695等于 bn
那么如果我们不停的用(an - bn) 和 bn 相减，那么最终肯定n会剩下来，除非 一开始 a和b就相等
所以，我们也就知道了 an和bn 的GCD 以及 (an - bn) 和 bn 的 GCD 肯定是一样的，
也就是，我们把一个问题简化成了更小的问题，同样的思路，如果用 an % bn，其实和an - bn的意思是一样的
只不过加速了这个过程

返回gcd(b % a, a)的作用就是确保把更小的数放到前面


```java
public int gcd(int a, int b) {
    if (a == 0) {
        return b
    }
    return gcd(b % a, a);
}
```
Lowest Common Multiple 就是 用 a 和 b的乘积 除以 他们的 GCD 就可以了

### swap

不使用临时变量的情况下swap2个integer

```java

x = x ^ y;
y = x ^ y;
x = x ^ y;
```

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
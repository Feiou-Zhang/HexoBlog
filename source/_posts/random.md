---
title: 随机
date: 2018-02-26 13:30:16
tags: [Random]
categories: [Algorithm]
---
水塘抽样， 随机， Reservoir Sampling

### shuffle
随机是很简单的，但等概率随机就稍微有点tricky，
比如说，我们要把[A, B, C] 三张牌shuffle 一下，要求每一种排列有`相同机会`出现
我们来看下面这个算法有什么问题

```java
    public int[] shuffle() {
        Random random = new Random();
        int[] shuffled = new int[nums.length];
        System.arraycopy(nums, 0, shuffled, 0, nums.length);
        for (int i = 0; i < nums.length; ++i) {
            int random_index = random.nextInt(nums.length);
            swap(shuffled, i, random_index);
        }
        return shuffled;
    }
```
这个算法看似没有什么问题，但其实这个算法，并不会使每一种排列有相同机会出现
用一个最简单最直观的方法来证明，

3个元素的全排列 一共有3！ 也就是6种，
但如果，每个元素都随机和和任何一个元素的index 交换的话，一共有27种排列组合。（见下图）
那么27种组合，无论如何都不可能平均分到这6种permutation上
![错误随机](shuffle_v1.png)

那么怎样才是正确的 等概率 随机算法呢？ 那就是 Knuth Shuffle 算法 （努斯洗牌）

其实这个算法的逻辑并不复杂，就是每个元素只能从0到这个元素的index之间 取随机数（见下图）
![正确随机](shuffle_v2.png)

[reference](https://yjk94.wordpress.com/2017/03/17/%E6%B4%97%E7%89%8C%E7%9A%84%E6%AD%A3%E7%A1%AE%E5%A7%BF%E5%8A%BF-knuth-shuffle%E7%AE%97%E6%B3%95/)
```java
for (int i = 0; i < nums.length; ++i) {
    int random_index = random.nextInt(i + 1);
    swap(shuffled, i, random_index);
}
```

#### 384. Shuffle an Array
```java
/**
 * 题意：给一个无重数组，要求，等概率的重新组合这个数组
 * */
public class ShuffleanArray0384 {
    /** time O(n) space O(n) 方法：水塘抽样
     * 思路：当前元素只能和0到当前元素之间的随机index 交换。
     * 优化：
     * */
    int[] nums;
    public ShuffleanArray0384(int[] nums) {
        this.nums = nums;
    }

    /** Resets the array to its original configuration and return it. */
    public int[] reset() {
        return nums;
    }

    /** Returns a random shuffling of the array. */
    public int[] shuffle() {
        Random random = new Random();
        int[] shuffled = new int[nums.length];
        System.arraycopy(nums, 0, shuffled, 0, nums.length);
        for (int i = 0; i < nums.length; ++i) {
            int random_index = random.nextInt(i + 1);
            swap(shuffled, i, random_index);
        }
        return shuffled;
    }
    private void swap(int[] nums, int a, int b) {
        int tem = nums[a];
        nums[a] = nums[b];
        nums[b] = tem;
    }
}
```

### n选1

等概率选择一个，其实相当于，找一个方法，让所有target 被留下来的概率相等，
比如现在有3个target，遍历到第一个的时候，我们发现有一个，就选这个 选择第一个的概率是1
发现第二个的时候，选第二个的概率是1/2, 那么舍弃第一个的概率也是1/2,
也就是说第一个留下来的概率也变成了1/2，当发现第三个的时候，选第三个的概率是1/3
那么选之前留下来的数的概率就是2/3，乘以之前的1/2，就也变成了1/3

#### 398. Random Pick Index
```java
/**
 * 题意：给一个有重无序数组，给一个target值，这个值，在这个数组里出现了1-n次
 * 求等概率随机返回这个数的index
 * */
public class RandomPickIndex0398 {
    /** time O(n) space O(n) 方法：水塘抽样
     * 思路：用一个count来记录target 出现的次数，每一次用1/count来表示该index留下的概率
     * random.nextInt(++count) == 0 的意思就是 0 在 0 到count这个范围随机的概率是 1/count
     * 优化：
     * */
    int[] nums;
    public RandomPickIndex0398(int[] nums) {
        this.nums = nums;
    }

    public int pick(int target) {
        int res = -1;
        int count = 0;
        Random random = new Random();
        for (int i = 0; i < nums.length; ++i) {
            if (nums[i] != target) {
                continue;
            }
            if (random.nextInt(++count) == 0) {
                res = i;
            }
        }
        return res;
    }
}
```

#### 382. Linked List Random Node
```java
/**
 * 题意：给一个单链表，要求返回一个随机的节点的值，而且随机必须是等概率的
 * follow up： 如果链表很长，而且并不知长度，如何不用额外空间做
 * */
public class LinkedListRandomNode0382 {
    /** time O() space O() 方法：
     * 思路：
     * 优化：
     * */
    ListNode root;
    private Random random;
    public LinkedListRandomNode0382(ListNode head) {
        root = head;
        random = new Random();
    }

    /** Returns a random node's value. */
    public int getRandom() {
        ListNode curr = root;
        int count = 0;
        int res = -1;
        
        while (curr != null) {
            res = random.nextInt(++count) == 0 ? curr.val : res;
            curr = curr.next;
        }
        return res;
    }
}
```
### n选k

```java
/**
 * 题意： 要求等概率的选取k个数从一个数组里。
 * */
public class ReservoirSampling {
    /** time O(n) space O(1) 方法：水塘抽样
     * 思路：前k个数，直接拿，概率都是1/n, 第k+1个数开始，
     * 优化：
     * */
    public int[] selectKItems(int[] stream, int k) {
        Random random = new Random();
        int[] sample = new int[k];
        for (int i = 0; i < k; ++i) {
            sample[i] = stream[i];
        }
        for (int i = k; i < stream.length; ++i) {
            int rmd = random.nextInt(i + 1);
            if (rmd < k) {
                sample[k] = stream[i];
            }
        }
        return sample;
    }
}
/** 相对最make sense的一个解释，但不是很理解其实 
 * 初始情况 n <= k：此时每个元素留下的概率均为1。当 n = k+1 时，第k+1个元素留下的概率为k/(k+1)，
 前k个元素留下的概率均为：k/k * (1 - k/(k+1) * 1/k) = k/(k+1)，即上一步留下的概率乘以这一步留下的概率。
 假设 n = m 时，每个元素留下的概率均为 k/n = k/m。那么，当 n = m+1 时，第m+1个元素留下的概率为k/(m+1)，
 前m个元素留下的概率均为：k/m * (1 - k/(m+1) * 1/k) = k/(m+1)，其中：k/m为上一步留下来的概率，
 k/(m+1) * 1/k 为这一步不能留下来的概率（第m+1个留下来，同时池中一个元素被踢出的概率）。
 综上可知，算法成立。
 http://www.echojb.com/computer/2017/02/22/330179.html
 */
```
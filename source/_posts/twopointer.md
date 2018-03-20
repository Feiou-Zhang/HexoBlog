---
title: 双指针，窗口类，对撞类，并行类
date: 2018-02-10 14:04:11
tags: [Two Pointer]
categories: [Algorithm]
---
### 窗口类问题

这类问题需要注意的几个点：
0. 首先窗口类问题，如果是数组的话，里面的数都需要是 正数
1. 什么时候更新最大最小值，有些题目可以放到最后无脑检查，有些则是有条件的更新，需要看清题目
2. 右移左指针一般都是循环，需要注意，循环结束后是否需要再移动一次，或者再更新一个map，具体看题目
3. 要注意，还有根本找不到答案的情况，同时注意的 min，max的初始

#### 3. Longest Substring Without Repeating Characters
```java
/**
 * Given a string, find the length of the longest substring without repeating characters.

 Examples:

 Given "abcabcbb", the answer is "abc", which the length is 3.

 Given "bbbbb", the answer is "b", with the length of 1.

 Given "pwwkew", the answer is "wke", with the length of 3. Note the answer must be a substring
 "pwke" is a subsequence and not a substring.*/
public class LongestSubstringWithoutRepeatingCharacters0003 {
    /** time O(n) space O(1) 方法：双指针，窗口类
     * 思路：用一个map记录窗口内的字母，然后用头尾指针来确定最长无重子序列的两个边界
     * 用右指针遍历（探索）字符串，如果碰见不在map里面的字母，指针右移，更新 max
     * 当右指针找到一个map里面有的字母的时候，用左指针向右移 去找新窗口的左边界，
     * 左边界就是和右指针字母相同 字母的后一位
     *
     * 具体写还是需要举例画图分析才不容易出错。容易出错的点在于找左边界，条件就是当左右指针上的字母不一样时
     * map里都应该删除左指针上的字母，然后右移左指针，知道一样的时候，跳出循环，这时候，需要再右移一次左指针
     * 原因就是这时候，这时候左右指针还指向了同一个字母，而左边界应该是和右指针字母相同 字母的后一位
     *
     * 小优化，如果longest已经等于256了，直接返回就可以，因为不能有更长的了
     * */
    public int lengthOfLongestSubstring(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        int longest = 1;
        int[] map = new int[256];
        for (int left = 0, right = 0; right < s.length(); ++right) {
            if (map[s.charAt(right)] == 0) {
                ++map[s.charAt(right)];
            } else {
                while (s.charAt(left) != s.charAt(right) && map[s.charAt(right)] > 0) {
                    --map[s.charAt(left++)];
                }
                ++left;
            }
            longest = Math.max(longest, right - left + 1);
        }
        return longest;
    }
}
```

#### 209. Minimum Size Subarray Sum
```java
/**
 * Given an array of n positive integers and a positive integer s, find the minimal length of
 * a contiguous subarray of which the sum ≥ s. If there isn't one, return 0 instead.

 For example, given the array [2,3,1,2,4,3] and s = 7,
 the subarray [4,3] has the minimal length under the problem constraint.

 click to show more practice.

 More practice:
 If you have figured out the O(n) solution, try coding with time complexity O(n log n).*/
public class MinimumSizeSubarraySum0209 {
    /** time O(n) space O(1) 方法：双指针，窗口
     * 思路：用左右指针来 标记 最小窗口的边界，如果当前sum小于target，sum加右指针上的数
     * while sum大于target的时候，右移 左指针，并用sum减去左指针上的数，直到sum小于target
     * 注意：1. 只有在sum 大于等于s的时候才能更新minSub，所以要放在while循环前面
     *      2. 最后要检查下，如果整个数组和都小于s的时候，means minSub还是null的时候，直接返回0
     * 优化：sum加右指针上的数可以不用检查sum是不是小于target，因为每次进入for循环，sum肯定小于s
     * */
    public int minSubArrayLen(int s, int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int sum = 0;
        Integer minSub = null;
        for (int left = 0, right = 0; right < nums.length; ++right) {
            if (sum < s) {
                sum += nums[right];
            }
            while (sum >= s) {
                minSub = minSub == null ? right - left + 1 : Math.min(minSub, right - left + 1);
                sum -= nums[left++];
            }
        }
        return minSub == null ? 0 : minSub;
    }
}
```

#### 76. Minimum Window Substring
```java
/**
 * Given a string S and a string T, find the minimum window in S
 * which will contain all the characters in T in complexity O(n).

 For example,
 S = "ADOBECODEBANC"
 T = "ABC"
 Minimum window is "BANC".

 Note:
 If there is no such window in S that covers all characters in T, return the empty string "".

you are guaranteed that there will always be only one unique minimum window in S.

 */
public class MinimumWindowSubstring0076 {
    /** time O(n) space O(1) 方法：双指针，窗口
     * 思路：用map记录字符串T的词频（字频），左右指针标记窗口的边界, count 初始为t的长度
     * 1. 当右指针的字母在map里的字频 - 1 之后还 大于等于0 的时候，count--；
     * 意思就是找到了一个qualified字母，因为如果如果之前是0 的话，说明改字母不在map里或者是重复字母
     * 2. 当count等于0的时候，右移左指针，当左指针的字母在map里面的字频 + 1之后大于0的时候，count++
     * 意思就是，如果+1 大于0，说明之前肯定至少是0，负数的话，该字母必定不在T里面，或者在T里面但目前重复
     * 注意： 更新minSub 必须 在count等于0 的时候，且移动左指针之前
     * 优化：
     * */
    public String minWindow(String s, String t) {
        if (s == null || t == null || s.length() < t.length()) {
            return "";
        }
        int[] map = new int[256];
        Integer minSub = null;
        int count = t.length();
        for (char c : t.toCharArray()) {
            ++map[c];
        }
        int start = 0;
        int end = 0;
        for (int left = 0, right = 0; right < s.length(); ++right) {
            if (--map[s.charAt(right)] >= 0) {
                --count;
            }
            while (count == 0) {
                if (minSub == null || right - left + 1 < minSub) {
                    minSub = right - left + 1;
                    start = left;
                    end = right;
                }
                if (++map[s.charAt(left++)] > 0) {
                    ++count;
                }
            }
        }
        return minSub == null ? "" : s.substring(start, end + 1);
    }
}
```
#### 159. Longest Substring with At Most Two Distinct Characters

```java
/**
 * find the length of the longest substring T that contains at most 2 distinct characters.

 For example, Given s = “eceba”,

 T is "ece" which its length is 3.*/
public class LongestSubstringwithAtMostTwoDistinctCharacters0159 {
    /** time O(n) space O(1) 方法：双指针，窗口
     * 思路：用map记录当前窗口内的字母和词频，用一个count来记录当前有几个unique的字母
     * 循环遍历字符串，用右指针探索，++ map里右指针上的字母，如果该字母词频是0，++count
     * 当count大于2的时候，循环 右移左指针，--map里左指针上的字母，
     * 直到 当有新的字母词频是0 的时候，--count
     * 结尾更新长度
     * 优化：
     * */
    public int lengthOfLongestSubstringTwoDistinct(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        int[] map = new int[256];
        int count = 0;
        int longest = 1;
        for (int left = 0, right = 0; right < s.length(); ++right) {
            if (++map[s.charAt(right)] == 1) {
                ++count;
            }
            while (count > 2) {
                if (--map[s.charAt(left++)] == 0) {
                    --count;
                }
            }
            longest = Math.max(longest, right - left + 1);
        }
        return longest;
    }
}

```

#### 340. Longest Substring with At Most K Distinct Characters

```java
/**
 *find the length of the longest substring T that contains at most k distinct characters.

 For example, Given s = “eceba” and k = 2,

 T is "ece" which its length is 3.

 */
public class LongestSubstringwithAtMostKDistinctCharacters0340 {
    /** time O(n) space O(1) 方法：双指针，窗口
     * 思路：用map记录当前窗口内的字母和词频，用一个count来记录当前有几个unique的字母
     * 循环遍历字符串，用右指针探索，++ map里右指针上的字母，如果该字母词频是0，++count
     * 当count大于k的时候，循环 右移左指针，--map里左指针上的字母，
     * 直到 当有新的字母词频是0 的时候，--count
     * 结尾更新长度
     * 优化：如果k大于等于s的长度，直接返回s的长度
     * */
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        if (s == null || s.length() == 0 || k == 0) {
            return 0;
        }
        if (k >= s.length()) {
            return s.length();
        }
        int[] map = new int[256];
        int count = 0;
        int longest = 1;
        for (int left = 0, right = 0; right < s.length(); ++right) {
            if (++map[s.charAt(right)] == 1) {
                ++count;
            }
            while (count > k) {
                if (--map[s.charAt(left++)] == 0) {
                    --count;
                }
            }
            longest = Math.max(longest, right - left + 1);
        }
        return longest;
    }
}

```

#### 30. Substring with Concatenation of All Words

`这题非常麻烦，需要仔细做，多做`

```java
/**
 * You are given a string, s, and a list of words, words, that are all of the same length.
 * Find all starting indices of substring(s) in s that is a concatenation of each word
 * in words exactly once and without any intervening characters.

 For example, given:
 s: "barfoothefoobarman"
 words: ["foo", "bar"]

 You should return the indices: [0,9].
 (order does not matter).*/
public class SubstringwithConcatenationofAllWords0030 {
    /** time O(n * k) space O(k) 方法：双指针，窗口
     * 思路：1.遍历字符串S，k次，k为word的长度，因为k此之后，其实就是重复前面的计算了
     * 2. 用map统计words的词频，count初始为words的长度，
     * 3. 每次进入外循环， 新建一个map，因为每次都和之前没关系
     * 4. 右指针探索，每次向前移动k步，所以右指针的区间是0 到 s长度-单词的长度，
     * 5. 左指针的区间是0 到 s - 单词的长度*单词的个数
     * 6. 当map中有右指针上的单词的时候，把该单词加入临时map里面，并更新词频
     *    如果词频大于原map中的词频的话，要不断的把左指针的单词从临时map 缩减词频并--count
     * 7，直到右指针单词的词频在2个map中一样的时候结束，然后继续++count并查看 count是否
     *    到达单词个数的长度，如果是，把左指针加入结果，右移左指针，更新临时map和count，
     * 8. 当map中没有右指针上的单词的时候，同时移动左右指针，重置 map 和 count
     *    因为是找连续的substring 而不是序列，所以，只要有不符合的，就要跳过
     * 优化：
     * */
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        if (s == null || s.length() == 0 || words == null || words.length == 0) {
            return res;
        }
        Map<String, Integer> map = new HashMap<>();
        for (String word : words) {
            map.put(word, map.getOrDefault(word, 0) + 1);
        }
        int wordLength = words[0].length();
        int numberOfWords = words.length;
        for (int i = 0; i < wordLength; ++i) {
            Map<String, Integer> tmp = new HashMap<>();
            for (int left = i, right = i, count = 0; right <= s.length() - wordLength &&
                    left <= s.length() - wordLength * numberOfWords; right += wordLength) {
                String curr = s.substring(right, right + wordLength);
                if (map.containsKey(curr)) {
                    tmp.put(curr, tmp.getOrDefault(curr, 0) + 1);
                    while (tmp.get(curr) > map.get(curr)) {
                        String pre = s.substring(left, left + wordLength);
                        tmp.put(pre, tmp.get(pre) - 1);
                        --count;
                        left += wordLength;
                    }
                    if (++count == numberOfWords) {
                        res.add(left);
                        String pre = s.substring(left, left + wordLength);
                        tmp.put(pre, tmp.get(pre) - 1);
                        --count;
                        left += wordLength;
                    }
                } else {
                    tmp.clear();
                    count = 0;
                    left = right + wordLength;
                }
            }
        }
        return res;
    }
}

```
#### 438. Find All Anagrams in a String

`同样是很麻烦的题，虽然被leetcode标成easy`

```java
/**
 * Given a string s and a non-empty string p, find all the start indices of p's anagrams in s.

 Strings consists of lowercase English letters only and the length is less than 20,100.

 The order of output does not matter.

 Example 1:

 Input:
 s: "cbaebabacd" p: "abc"

 Output:
 [0, 6]

 Explanation:
 The substring with start index = 0 is "cba", which is an anagram of "abc".
 The substring with start index = 6 is "bac", which is an anagram of "abc".
 Example 2:

 Input:
 s: "abab" p: "ab"

 Output:
 [0, 1, 2]

 Explanation:
 The substring with start index = 0 is "ab", which is an anagram of "ab".
 The substring with start index = 1 is "ba", which is an anagram of "ab".
 The substring with start index = 2 is "ab", which is an anagram of "ab".*/
public class FindAllAnagramsinaString0438 {
    /** time O(n) space O(1) 方法：双指针，窗口
     * 思路：用map记录词频，左右指针标记边界，count初始为p的长度
     * 如果 -- 右指针在map里词频 大于等于 0，说明该单词在p里， --count
     * 当 count为0的时候，左指针 右移一位，
     * 但当右指针在map的词频小于0的时候，要一直增加左指针的词频，并右移左指针，
     * 注意，只有当左指针的词频大于0的时候才更新count，因为有重复单词的情况
     * 优化：
     * */
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> res = new ArrayList<>();
        if (s == null || p == null || s.length() < p.length()) {
            return res;
        }
        int[] map = new int[26];
        for (char c : p.toCharArray()) {
            ++map[c - 'a'];
        }
        int count = p.length();
        for (int left = 0, right = 0; right < s.length(); ++right) {
            if (--map[s.charAt(right) - 'a'] >= 0) {
                if (--count == 0) {
                    res.add(left);
                    ++map[s.charAt(left++) - 'a'];
                    ++count;
                }
            }
            while (map[s.charAt(right) - 'a'] < 0) {
                if (++map[s.charAt(left++) - 'a'] > 0) {
                    ++count;
                }
            }
        }
        return res;
    }
}
```

### 对撞类

#### 611. Valid Triangle Number

```java

/**
 * 题意：给一个无序数组，求这个数组可以有多少种组合组成三角形，元素不可复用
 * */
public class ValidTriangleNumber0611 {
    /** time O(n^2) space O(1) 方法：3 sum
     * 思路：先排序，然后按3 sum的方法来做，但又有一点区别，
     * 3 sum的时候，左右指针，只有其中一个可以移动，这点也保证了算法的正确性，
     * 单这道题如果完全按照3sum来做，当 12边小于3边的时候，右移2边和左移3边其实都有可能是正确的
     * 所以，要改动一下，确保只需要移动一条边，仔细想一下，不难想到，可以倒着做，每次固定最后一边
     * 优化：
     * */
    public int triangleNumber(int[] nums) {
        if (nums == null || nums.length < 3) {
            return 0;
        }
        Arrays.sort(nums);
        int count = 0;
        int firstPositive = 0;
        while (firstPositive < nums.length && nums[firstPositive] < 1) {
            ++firstPositive;
        }
        for (int thirdEdge = nums.length - 1; thirdEdge > firstPositive + 1; --thirdEdge) {
            int firstEdge = firstPositive;
            int seconEdge = thirdEdge - 1;
            while (firstEdge < seconEdge) {
                if (nums[firstEdge] + nums[seconEdge] <= nums[thirdEdge]) {
                    ++firstEdge;
                } else {
                    count += seconEdge - firstEdge;
                    --seconEdge;
                }
            }
        }
        return count;
    }
}
```

#### 382. Triangle Count (LintCode)

```java
/**
 * Given an array of integers, how many three numbers can be found in the array,
 * we can build an triangle whose three edges length is the three numbers we find?

 Example
 Given array S = [3,4,6,7], return 3. They are:

 [3,4,6]
 [3,6,7]
 [4,6,7]
 Given array S = [4,4,4,4], return 4. They are:

 [4(1),4(2),4(3)]
 [4(1),4(2),4(4)]
 [4(1),4(3),4(4)]
 [4(2),4(3),4(4)]
 */
public class TriangleCount0382 {
    /** time O(n^2) space O(1) 方法：先排序，然后对撞双指针
     * 首先组成三角形的3条边的规则就是任意2边和要大于第三边
     * 错误思路： 像3sum一样就错了，因为当前2边小于第三边的时候，++第二边和--第三边都可能正确
     * 双指针肯定是要保证每次只移动一个指针的，如果2个都可以走，就有机会miss正确答案
     * 思路：正确的想法就是以第三边边为基准，外循环为第三边，把一条边放index0，第二边放第三边前一位
     * 这样，就可以保证一二边只有一边走，因为这种情况，不符合的话，只会是一二边和小于第三边，
     * 一三，肯定不会小于二。 二三就更不会小于一
     * 具体的做法就是，如果一二边小于等于第三边，一边右移，
     * 如果一二边大于第三边的话，后面所有的一边肯定都符合，因为后面的一边肯定都大于当前一边
     * 优化：
     * */
    public int triangleCount(int[] s) {
        if (s == null || s.length < 3) {
            return 0;
        }
        int count = 0;
        Arrays.sort(s);
        int third = 0;
        //skip all zeros
        while (third < s.length && s[third] == 0) {
            ++third;
        }
        third = third <= 2 ? 2 : third;
        for (; third < s.length; ++third) {
            int first = 0;
            int second = third - 1;
            while (first < second) {
                if (s[first] + s[second] <= third) {
                    ++first;
                } else {
                    count += second - first;
                    --second;
                }
            }
        }
        return count;
    }
}
```

### partition 类

```java
/**
 * 题意：给一个无序数组，里面有0，1，2三种元素，代表3种颜色
 * 要求，排序，
 * */
public class SortColors0075 {
    /** time O(n) space O(1) 方法：3-way partition
     * 思路：首先想到的就是桶排序，需要O(K)空间
     * 优化：3-way partition
     * 3-way partition的方法就是，用3个指针，left，right 和 i
     * 如果nums[i] 比1小，那么i和left swap，反之 i 和 right swap
     * 如果nums[i] 等于1，那么只++i，
     * 注意：nums[i] 比1小 的时候。swap的时候，要同时increment i和left
     * 但比1大的时候，只decrement right， 原因就是换到左边的数，位置是确定了的，
     * 但从右边换过来的数，还有可能是比1大的数，所以，要继续检查
     * 这样，可以用left保证，左边都比一小，right保证右边都比1大，
     * */
    public void sortColors(int[] nums) {
        if (nums == null || nums.length < 2) {
            return;
        }
        int left = 0;
        int right = nums.length - 1;
        int i = 0;
        while (i <= right) {
            if (nums[i] < 1) {
                swap(nums, i++, left++);
            } else if (nums[i] > 1) {
                swap(nums, i, right--);
            } else {
                ++i;
            }
        }
    }
    private void swap (int[] nums, int a, int b) {
        int tem = nums[a];
        nums[a] = nums[b];
        nums[b] = tem;
    }
}

```
#### 143. Sort Colors II （LintCode）
```java
/**
 * 题意：排序 无序数组，里面有k种元素，1到k*/
public class SortColorsII0143LintCode {
    /** time O() space O() 方法：桶排序，或者k way partition
     * 思路：桶排序直观，但需要额外空间，
     * 优化：如果需要inplace做的话，需要用partition，
     * 方法就是 start 和end 记录当前的2种颜色
     * 左右指针来做 two way partition，每次做完一次，更新下2个颜色
     * */
    public void sortColors2(int[] colors, int k) {
        if (colors == null || colors.length < 2) {
            return;
        }
        int startColor = 1;
        int endColor = k;
        int left = 0;
        int right = colors.length - 1;
        int i = 0;
        while (startColor < endColor) {
            i = left;
            while (i <= right) {
                if (colors[i] == startColor) {
                    swap(colors, i++, left++);
                } else if (colors[i] == endColor) {
                    swap(colors, i, right--);
                } else {
                    ++i;
                }
            }
            ++startColor;
            --endColor;
        }
    }
    private void swap (int[] nums, int a, int b) {
        int tem = nums[a];
        nums[a] = nums[b];
        nums[b] = tem;
    }
}

```
### 假窗口类
这类题看起来很像是窗口类问题，但需要注意的是，如果数组里面有负数，是不能用双指针窗口来做的
而且通常 这类题 可以先想用hashmap 来做

#### 325. Maximum Size Subarray Sum Equals k

```java
/**
 * Given an array nums and a target value k, find the maximum length of a subarray
 * that sums to k. If there isn't one, return 0 instead.

 Note:
 The sum of the entire nums array is guaranteed to fit within the 32-bit signed integer range.

 Example 1:
 Given nums = [1, -1, 5, -2, 3], k = 3,
 return 4. (because the subarray [1, -1, 5, -2] sums to 3 and is the longest)

 Example 2:
 Given nums = [-2, -1, 2, 1], k = 1,
 return 2. (because the subarray [-1, 2] sums to 1 and is the longest)

 Follow Up:
 Can you do it in O(n) time?*/
public class MaximumSizeSubarraySumEqualsk0325 {
    /** 猛一看，这题 很像是用窗口和双指针能解决的问题，但是要注意，这里面的数不全是正数！
     * 反而这道题很想two sum，暴力一点的想法就是，开个数组保存前缀和，然后如何第一次前缀和里
     * 就有k的话，肯定最后那个是最长的，然后把所有前缀和都减去sum[0],这样就好比从第二个index
     * 开始的subarray，然后以此类推，但这样，需要n^2的时间
     * time O(n) space O(n) 方法：hashmap, k: prefix sum v : 前面有几个数
     * 思路：从第二个数开始遍历，如果k减当前前缀和在map里面的话，更新最大值
     * 此时最大值是当前index - map中key为sum-k的值，也就是这个差之前有几个数
     * 如果map不包含这个差，那么就把 当前前缀和 以及index加入map，（这里不是把差 加入map）
     * 注意：这里必须是 map里面不含这个key的时候才加，因为如果包含了key,前面的index肯定优先于
     * 后面的index，在求最长结果的情况下
     * 优化：
     * */
    public int maxSubArrayLen(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int maxlength = 0;
        Map<Integer, Integer> prefixSumMap = new HashMap<>();
        prefixSumMap.put(0, -1);
        int sum = 0;
        for (int i = 0; i < nums.length; ++i) {
            sum += nums[i];
            //这里，如果k和第一个数字一样的话，结果就Miss了，所以应该给map初始一个 (0, -1)
            if (prefixSumMap.containsKey(sum - k)) {
                maxlength = Math.max(maxlength, i - prefixSumMap.get(sum - k));
            }
            if (!prefixSumMap.containsKey(sum)) {
                prefixSumMap.put(sum, i);
            }
        }
        return maxlength;
    }
}
```

#### 1. Two Sum

```java
/**
 * Given an array of integers, return indices of the two numbers add up to a specific target.

 You may assume there is exactly one solution, and you may not use the same element twice.

 Example:
 Given nums = [2, 7, 11, 15], target = 9,

 Because nums[0] + nums[1] = 2 + 7 = 9,
 return [0, 1].*/
public class TwoSum0001 {
    /** time O(n) space O(n) 方法：
     * 思路：遍历数组，如果k减去当前数在map里面的话，直接找到了答案，否则，把当前数和下标存入map
     * 优化：
     * */
    public int[] twoSum(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return new int[]{-1, -1};
        }
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; ++i) {
            if (map.containsKey(target - nums[i])) {
                return new int[] {map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        return new int[]{-1, -1};
    }
}

```


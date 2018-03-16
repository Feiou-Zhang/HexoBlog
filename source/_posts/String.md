---
title: String
date: 2018-03-12 23:18:58
tags: [String]
categories: [Algorithm]
---
### Common methods
​
```java
charAt(int index)
​
codePointAt(int index)
​
substring(int start, int end) //s will be lower case
//the start index will be included and the end index will be not.
//same as the delete method from the StringBuilder class.
​
substring(int start)
```
​
### StringBuilder:
​
> there is no method to clear or empty a StringBuilder, so, how to empty a StringBuilder?
```java
//unlike a String, you can not do the following two ways
sb = "";
sb = null;
//but there are other three ways you can achieve the goal.
sb = new StringBuilder(); //slowest way
sb.delete(0, sb.length());
sb.setLength(0);
```
​
Most of the methods will return a StringBuilder type:
​
```java
用append方法的时候，不要使用append（“a" + "b"）
尽量用append("a").append("b")
append(char/char[]/String/StringBuilder)
​
delete(int start, int end), deleteCharAt(int index),
​
insert(int index, char c/String str)
​
replace(int start, int end, String str)
​
reverse()
```
​
`However`
​
```java
setCharAt(int index,char c) will return a void type. 
will be as same as the replace method of String
```
​
### String:
​
​
> compareTo(String str): this could be a little bit tricky, it first compares the unique code for each character until the first difference no matter which one gets the longer length. If all the characters are the same, it will compare the length and return the difference.
​
There is a `contains` method but need CharSequence type of parameter.
​
```java
concat(String str)
//will be as same as +, but will not be less efficient than the append method of the StringBuilder
​
startsWith(String str), endsWith(String str)
//be careful the 's' and these two methods ONLY take String type of parameter
// will not take char or char[]
​
indexOf(int ch, char ch, String str)
//will do the String Matching for you, but it is using the brute force algorithm. 
​
replace(char old, char new)
//will be different with the replace of the StringBuilder Class
​
valueOf()
//this is the ONLY one static method in the String class
​
```
​
​
#### 395. Longest Substring with At Least K Repeating Characters
```java

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * 题意：给定一个字符串s（只包含小写字母），定义子串T，其中所有字符出现次数均不少于k次，返回子串T的最大长度。
 * */
public class LongestSubstringwithAtLeastKRepeatingCharacters0395 {
    /** time O(n^2) 平均 nlogn 每次一半， space O(1) 方法：递归，分制
     * 思路：把大问题拆分成小问题 ，所以递归函数需要传入子问题的左右边界
     * 首先统计词频，找到所有词频小于k的字母的index，然后问题就可以拆分成从start到这个index已经后边的部分
     * 优化：可以记录一个 全局最大值，这样，递归的时候，如果整体区间的最大长度已经小于全局最大值，就直接返回
     * */
    public int longestSubstring(String s, int k) {
        if (s == null || s.length() == 0 || k > s.length()) {
            return 0;
        }
        if (k == 1) {
            return s.length();
        }
        return helper(s.toCharArray(), k, 0, s.length() - 1, new int[] {0});
    }
    private int helper(char[] chars, int k, int start, int end, int[] longest) {
        if (start >= end || end - start + 1 <= longest[0]) {
            return 0;
        }
        int[] count = new int[26];
        for (int i = start; i <= end; ++i) {
            ++count[chars[i] - 'a'];
        }
        for (int i = 0; i < count.length; ++i) {
            if (count[i] > 0 && count[i] < k) {
                int lastPosition = start;
                for (int j = start; j <= end; ++j) {
                    if (chars[j] == (i + 'a')) {
                        longest[0] = Math.max(longest[0], helper(chars, k, lastPosition, j - 1, longest));
                        lastPosition = j + 1;
                    }
                }
                longest[0] = Math.max(longest[0], helper(chars, k, lastPosition, end, longest));
                return longest[0];
            }
        }
        return end - start + 1;
    }
}

```
#### 392. Is Subsequence
```java
/**
 * 题意：判断字符串s 是不是字符串t的子序列
 * */
public class IsSubsequence0392 {
    /** time O(n) space O(1) 方法：
     * 思路：扫一遍2个字符串，依次看小字符串里的每个字符是否在大字符串里出现（按顺序）
     * 优化：如果s很多，那么可以先预先处理t，建一个map，key 26个字母，value 是list of index
     * 这样，扫一遍s，依次查看每个字母在map里面的位置
     * */
    public boolean isSubsequence(String s, String t) {
        if (s == null || t == null || s.length() > t.length()) {
            return false;
        }
        int i = 0;
        int j = 0;
        int lenS = s.length();
        int lenT = t.length();
        while (i < lenS && j < lenT) {
            while (i < lenS && j < lenT && s.charAt(i) == t.charAt(j)) {
                ++i;
                ++j;
            }
            while (i < lenS && j < lenT && s.charAt(i) != t.charAt(j)) {
                if (lenT - (++j) < lenS - i) {
                    return false;
                }
            }
        }
        return i == lenS;
    }
}
```
#### 331. Verify Preorder Serialization of a Binary Tree
```java
/**
 * 题意：验证一个二叉树的先序遍历序列化 是否合法
 * 对于 二叉树 "9,3,4,#,#,1,#,#,2,#,6,#,#"
 *      _9_
       /   \
      3     2
     / \   / \
    4   1  #  6
   / \ / \   / \
  # #  # #  # #
 * */
public class VerifyPreorderSerializationofaBinaryTree0331 {
    /** time O(n) space O(n) 方法：
     * 思路：对于每一个非null节点，都应该有2个孩子，所以，遍历字符串
     * 每次遇到任何节点，count减1，然后每次遇到一个非null节点，count加2，
     * 这样，当任何时候，count小于0了，说明null节点有了孩子，肯定就不合法了 
     * 最后查看下，count是否等于0，如果不等于0，说明null节点少了，
     * 优化：
     * */
    public boolean isValidSerialization(String preorder) {
        if (preorder == null || preorder.length() == 0) {
            return false;
        }
        String[] nodes = preorder.split(",");
        int minimumNodes = 1;
        for (String node : nodes) {
            if (--minimumNodes < 0) {
                return false;
            }
            if (!node.equals("#")) {
                minimumNodes += 2;
            }
        }
        return minimumNodes == 0;
    }
}
```
---
title: 回文
date: 2018-03-30 22:22:17
tags: [Palindrome]
categories: [Algorithm]
---

#### 5. Longest Palindromic Substring
```java
/**
 * 题意：找出一个字符串中 最长的 子串回文
 * */
public class LongestPalindromicSubstring0005 {
    /** time O(n^2) space O(n^2) 方法：dp
     * 思路：判断一个字符串 的子串 i - j 是否是回文，那么其实就可以转化为，如果i 和 j 一样
     * 那么如果i 和 j 中间的部分是回文，那么i 到 j 也一定是回文，
     * 对于 i 和 j 一样的情况，可以优化为 只判断 length 大于3的情况，比如， aa， abc，一定是回文
     * 那么这个题就可以 用bottom up的方法，先得到所有小的子串是否是回文，然后cache结果
     * 所以状态转移方程也就自然而然的就是，dp(ij) 等于 dp(ij 中间的部分)
     * 优化：可以从每个index开始向左右两边扩散，当然要分aba 和 abba 两种情况扩散
     * 最坏时间复杂度一样，现实可能还更快点，可以做很多剪枝，而且不需要额外空间
     * */
    public String longestPalindrome(String s) {
        if (s == null || s.length() < 2) {
            return s;
        }
        int n = s.length();
        int start = 0;
        int end = 0;
        int longestLength = 0;
        boolean[][] isPali = new boolean[n][n];
        for (int i = 0; i < n; ++i) {
            for (int j = i; j >= 0; --j) {
                isPali[i][j] = s.charAt(i) == s.charAt(j) && (i - j < 3 || isPali[i - 1][j + 1]);
                if (isPali[i][j]) {
                    if (i - j + 1 > longestLength) {
                        start = j;
                        end = i;
                        longestLength = i - j + 1;
                    }
                }
            }
        }
        return s.substring(start, end + 1);
    }
    public String longestPalindrome1(String s) {
        if (s == null || s.length() < 2) {
            return s;
        }
        int n = s.length();
        char[] chars = s.toCharArray();
        String longestPali = "";
        for (int i = 0; i < n - 1 && (n - i) * 2 > longestPali.length(); ++i) {
            String s1 = getPali(chars, i, i);
            String s2 = chars[i] == chars[i + 1] ? getPali(chars, i, i + 1) : "";
            if (s1.length() > s2.length() && s1.length() > longestPali.length()) {
                longestPali = s1;
            }
            if (s2.length() > s1.length() && s2.length() > longestPali.length()) {
                longestPali = s2;
            }
        }
        return longestPali;
    }
    private String getPali(char[] chars, int l, int r) {
        for (; l >= 0 && r < chars.length && chars[l] == chars[r]; --l, ++r);
        return new String(chars, l + 1, r - l - 1);
    }
}

```

#### 132. Palindrome Partitioning II
```java
/**
 * 题意：求一共有几种cut法, 使得每个部分都是回文
 * */
public class PalindromePartitioningII0132 {
    /** time O(n^2) space O(n^2) 方法：双 dp
     * 思路：首先可以把这个题分成2部分来做，1.判断子串是否是回文，2，找最小分隔
     * 判断回文那个可以参考leetcode第五题，
     * 最小分割的话，如果 start 到 end 是回文的话，从 0 到 end 的最小分隔就是 start前一位的最小分隔次数 加1
     * 或者是当前end 已经得到的最小分隔次数， 取二者更小的 结果
     * 优化：
     * */
    public int minCut(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        int n = s.length();
        int[] cut = new int[n];
        boolean[][] isPali = new boolean[n][n];
        for (int i = 0; i < n; ++i) {
            cut[i] = i;
            for (int j = i; j >= 0; --j) {
                isPali[i][j] = s.charAt(i) == s.charAt(j) && (i - j < 3 || isPali[i - 1][j + 1]);
                if (isPali[i][j]) {
                    cut[i] = j == 0 ? 0 : Math.min(cut[i], cut[j - 1] + 1);
                }
            }
        }
        return cut[n - 1];
    }
}

```
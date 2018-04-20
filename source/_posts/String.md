---
title: String
date: 2018-03-12 23:18:58
tags: [String]
categories: [Algorithm]
---
​
#### 43. Multiply Strings
```java
/**
 * 题意：求2个 数字 字符串 的乘积
 * */
public class MultiplyStrings0043 {
    /** time O(mn) space O(m+n) 方法：
     * 思路：先建一个 数字 数组，长度为 2个字符串长度之和，然后模拟乘法运算，从低位开始算
     * 先算 2个 数字 的乘积 需要加上 上一次 的进位，
     * 然后当前低位的值 就是 乘积 % 10
     * 注意，高位上的值， 要 += 乘积 / 10， 因为 for循环上一次 高位上已经有值了
     * 最后form结果的时候，要先跳过开始的0，注意，不要把中间的0也跳过了
     * 优化：
     * */
    public String multiply(String num1, String num2) {
        if (num1 == null || num2 == null || num1.length() == 0 || num2.length() == 0) {
            return "0";
        }
        if (num1.equals("0") || num2.equals("0")) {
            return "0";
        }
        if (num1.equals("1")) {
            return num2;
        }
        if (num2.equals("1")) {
            return num1;
        }
        int[] result = new int[num1.length() + num2.length()];
        for (int i = num1.length() - 1; i >= 0; --i) {
            for (int j = num2.length() - 1; j >= 0; --j) {
                int lowerPosition = i + j + 1;
                int higherPosition = i + j;
                int product = (num1.charAt(i) - '0') * (num2.charAt(j) - '0') + result[lowerPosition];
                result[lowerPosition] = product % 10;
                result[higherPosition] += product / 10;
            }
        }
        StringBuilder res = new StringBuilder();
        int index = 0;
        while (result[index] == 0) {
            ++index;
        }
        for (; index < result.length; ++index) {
            res.append(result[index]);
        }
        return res.toString();
    }
}

```
#### 395. Longest Substring with At Least K Repeating Characters
```java
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
#### 214. Shortest Palindrome
```java
/**
 * 题意：给一个字符串，求，最少 '在前面' 加多少个字母可以把它变成一个回文，返回改回文
 * Given "abcd", return "dcbabcd".
 * */
public class ShortestPalindrome0214 {
    /** time O(n) space O(n) 方法：kmp
     * 思路：假设一个字符串 S前面加P 成为回文，首先，S是由2部分组成，内回文和逆序P，
     * 也就可以得到 PS = PQP' ，也就可以得到 S = QP' 这时候，如果给S后面加一个S'
     * 那么就可以得到 SS' = QP'PQ'，而且既然Q 是内回文，那么Q 和 Q' 就是一样的
     * 那么我们只要能找到原字符串S 的内回文Q 是什么，我们就能知道怎么加P'
     * 而这个Q 其实就是SS'的公共前后缀，这样我们也就可以用 kmp的next数组来找这个公共前后缀
     * 优化：少慢点的做法，也可以用二维dp来找到原S中最长的回文子串，需要以原首字母开头
     * */
    public String shortestPalindrome(String s) {
        if (s == null || s.length() < 2) {
            return s;
        }
        String ss = s + "#" + new StringBuilder(s).reverse();
        int n = ss.length();
        int[] next = new int[n];
        for (int i = 0, j = 1; j < n; ++j) {
            while (i > 0 && ss.charAt(i) != ss.charAt(j)) {
                i = next[i - 1];
            }
            if (ss.charAt(i) == ss.charAt(j)) {
                next[j] = ++i;
            }
        }
        return new StringBuilder(s.substring(next[n - 1])).reverse().append(s).toString();
    }
    public String dp(String s) {
        if (s == null || s.length() < 2) {
            return s;
        }
        int n = s.length();
        boolean[][] isPalindrome = new boolean[n + 1][n + 1];
        isPalindrome[0][0] = true;
        for (int i = n - 1; i >= 0; --i) {
            for (int j = i; j < n; ++j) {
                if (s.charAt(i) == s.charAt(j)) {
                    isPalindrome[i][j] = j - i < 3 || isPalindrome[i + 1][j - 1];
                }
            }
        }
        int longestPalindromeLength = 0;
        for (int i = n -1; i >= 0; --i) {
            if (isPalindrome[0][i]) {
                longestPalindromeLength = i + 1;
                break;
            }
        }
        //return new StringBuilder(s.substring(longestPalindromeLength)).reverse().toString() + s;
        StringBuilder res = new StringBuilder();
        for (int i = n - 1; i >= longestPalindromeLength; --i) {
            res.append(s.charAt(i));
        }
        return res.append(s).toString();
    }
}

```
---
title: 括号问题
date: 2018-04-04 22:18:34
tags: 
categories: [Algorithm]
---
括号问题一般都是用 `stack`， 包括一些算数的问题，很多也是用到 `stack`

#### 20. Valid Parentheses
```java

/**
 * 题意：给一组括号，有小中大三种，判断是否合法
 * */
public class ValidParentheses0020 {
    /** time O(n) space O(n) 方法：stack
     * 思路：用栈来判断，遇到open 压入栈，遇到close 就和 peek比较是否成对，
     * 最后看 栈是否为空
     * 优化：
     * */
    public boolean isValid(String s) {
        if (s == null || s.length() == 0 || s.length() % 2 != 0) {
            return false;
        }
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (isOpen(c)) {
                stack.push(c);
                continue;
            }
            if (isClose(c) && !stack.isEmpty() && isPair(stack.pop(), c)) {
                continue;
            }
            return false;
        }
        return stack.isEmpty();
    }
    private boolean isOpen(char c) {
        return c == '(' || c == '[' || c == '{';
    }
    private boolean isClose(char c) {
        return c == ')' || c == ']' || c == '}';
    }
    private boolean isPair(char open, char close) {
        return open == '(' && close == ')' ||
               open == '[' && close == ']' ||
               open == '{' && close == '}';
    }
}

```
#### 32. Longest Valid Parentheses
```java
/**
 * 题意：给一个字符串，只包含 开闭括号，求最长的合法括号组合的长度
 * */
public class LongestValidParentheses0032 {
    /** time O(n) space O(n) 方法：stack
     * 思路：用2个stack, 一个放括号，一个放括号的index，把合法的括号都从stack里面删掉
     * 剩下的都是不合法的，那么剩下的index 也就是合法括号的区间，更新最值就可以
     * 初始化要加一个-1 到index的stack里面， 以防止，最开始的部分是合法的 被删掉
     * 最后再加一个len - 1 到stack，以防止，周后面的部分是合法的，被删掉
     * 优化：
     * */
    public int longestValidParentheses(String s) {
        if (s == null || s.length() < 2) {
            return 0;
        }
        Deque<Character> parentheses = new ArrayDeque<>();
        Deque<Integer> indexes = new ArrayDeque<>();
        indexes.push(-1);
        for (int i = 0; i < s.length(); ++i) {
            char c = s.charAt(i);
            if (c == '(') {
                parentheses.push(c);
                indexes.push(i);
            }
            if (c == ')') {
                if (!parentheses.isEmpty() && parentheses.peek() == '(') {
                    parentheses.pop();
                    indexes.pop();
                } else {
                    parentheses.push(c);
                    indexes.push(i);
                }
            }
        }
        if (indexes.peek() != s.length()) {
            indexes.push(s.length());
        }
        int longest = 0;
        while (indexes.size() > 1) {
            longest = Math.max(longest, indexes.pop() - indexes.peek());
        }
        return longest - 1;
    }
}
```
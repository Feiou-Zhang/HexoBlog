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
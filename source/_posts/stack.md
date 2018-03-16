---
title: Stack
date: 2018-03-14 14:49:42
tags: [Stack]
categories: [Algorithm]
---
#### 316. Remove Duplicate Letters
```java

/**
 * 题意：给定一个只包含小写字母的字符串，从中移除重复字母使得每个字母只出现一次。你必须确保结果的字典序最小。
 * */
public class RemoveDuplicateLetters0316 {
    /** time O(n) space O(n) 方法：stack + 贪心
     * 1。首先统计词频，这样到时候，如果当前字母的count还大于0的时候，就可以放心删，反之，就必须要保留
     * 2. 用一个布尔数组来记录，当前栈里有哪些字母，如果遍历的时候，发现，栈里已经有该字母，就continue
     * 首先，结果不能重复，所以，肯定不能重复添加，其次，之前的局部结果，我们可以保证是最优，
     * 所以，后面相同的字母并不会影响最终结果
     * 3. 用一个栈来保存无序递增序列，
     * 遍历字符串，先--count，然后查看当前字母是否inUsed，如果当前栈不为空，而且，当前字母比栈顶小，
     * 并且栈顶的词频还大于0的时候，就pop栈顶，并比较inUsed 为unUsed，循环这一步
     * 然后把当前字母压入栈，并标记为inUsed，
     * 最后把结果倒序输出成新的字符串
     * 优化：
     * */
    public String removeDuplicateLetters(String s) {
        if (s == null || s.length() < 2) {
            return s;
        }
        Deque<Character> stack = new ArrayDeque<>();
        boolean[] used = new boolean[256];
        int[] count = new int[256];
        char[] chars = s.toCharArray();
        for (char c : chars) {
            ++count[c];
        }
        for (char c : chars) {
            --count[c];
            if (used[c]) {
                continue;
            }
            while (!stack.isEmpty() && c < stack.peek() && count[stack.peek()] > 0) {
                used[stack.pop()] = false;
            }
            stack.push(c);
            used[c] = true;
        }
        char[] res = new char[stack.size()];
        for (int i = res.length - 1; i >= 0; --i) {
            res[i] = stack.pop();
        }
        return new String(res);
    }
}

```
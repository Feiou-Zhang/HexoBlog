---
title: 位运算
date: 2017-03-12 11:32:01
tags: [Bit Manipulation]
categories: [Java 基础]
---
### Wiki
位操作是一种通过算法来处理比一个单词更短的数据块或其他数据块的行为。需要位操作的计算机编程任务包括低级设备控制、
错误检测和校正算法、数据压缩、加密算法和优化。对于大多数其他任务，现代编程语言允许程序员直接使用抽象而不是表示抽象的位。
执行位操作的源代码可以使用位操作: and(&), or(|)，xor(^)，not(~)，和 bit shift(>>, <<)。

在某些情况下，位操作可以消除或减少对数据结构进行循环的需要，并且可以提供多倍的速度，因为位操作是并行处理的，
但是代码会变得`更难`编写和维护。
### Details
#### Basics
位操作的核心是位操作运算符 and(&), or(|)，xor(^)，not(~)，和 bit shift(>>, <<)。
没有任何布尔运算符对应于位移的 xor(^)，但是有一个简单的解释。xor操作接受两个输入，如果两个输入一样，返回0，不一样返回1，
也就是说，如果两个输入都是1或者两个输入都是0，则返回0。
- Set union A | B (返回所有)
- Set intersection A & B （返回重叠部分）
- Set subtraction A & ~B ()
- Set negation ALL_BITS ^ A or ~A ()
- Set bit A |= 1 << bit ()
- Clear bit A &= ~(1 << bit) ()
- Test bit (A & 1 << bit) != 0 ()
- Extract last bit A&-A or A&~(A-1) or x^(x&(x-1)) ()
- Remove last bit A&(A-1) ()
- Get all 1-bits ~0 ()
### Examples
count给定数字的二进制表示中的1


本文是翻译这个帖子[原文](https://leetcode.com/problems/sum-of-two-integers/discuss/84278/A-summary:-how-to-use-bit-manipulation-to-solve-problems-easily-and-efficiently)
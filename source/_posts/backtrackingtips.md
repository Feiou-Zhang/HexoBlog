---
title: 回溯解题小技巧
date: 2018-03-04 21:41:47
tags: [回溯]
categories: [解题总结]
---

> 回溯问题种类很多，有多题都很类似，如果没有很理解题意，容易出些小差错，比如一个 combination sum，
解中，可以 或不可以 重复用元素，可以 或不可以 回头用之前的元素，之类的，题目稍微变一点，就可能变成另外一题
所以，这种题，读题必须读透。还有就是递归函数中，需不需要 用for 循环， 参数中需不需要传index，有时候也容易搞混

#### 递归需要传index
17. Letter Combinations of a Phone Number
递归下层用index+1，表示下一个数字，因为，最终的答案只能从第一个数字开始的，所以不用使用for循环
但对于每一个数字，还是需要循环来替换这个数字代表的所有字母

39. Combination Sum
当前元素可以重复使用，但不能回头用之前的元素，所以，虽然传入index，但递归的时候，不用+1
而且，组合中，其实可以 以任何元素开始，所以要用for循环

40. Combination Sum II, 216. Combination Sum III
当前元素不能重复使用，所以需要传入index，递归的时候，index + 1
而且，组合中，其实可以 以任何元素开始，所以要用for循环

77. Combinations, 78. Subsets, 90. Subsets II
因为不能选回头的元素，所以需要传入index来记录现在用到哪个元素了，而且，第一个元素可以是任何元素
所以要用for循环

78. Subsets, 90. Subsets II
subset的话，并不需要一个明确的退出条件，所以，在进入递归函数的时候，就可以先把当前结果加入到res

93. Restore IP Addresses
传index很好理解，因为下一层要从上一次断开的地方开始，而且，这个ip是必须要100%出自input字符串的
意思就是，必须从字符串的开头作为第一个segment，所以不需要用for循环，每次做1，2，3位数的判断既可

131. Palindrome Partitioning
需要传入一个左边界的index，然后for循环，找回文的右边界，然后下层递归的左边界就是上次的右边界
这题和Letter Combinations of a Phone Number有点像的地方就是，其实本来并不需要for循环
以为一定是要以input字符串的开始 为开始的，但由于右边界的范围无法确定，只能用for循环来确定边界

254. Factor Combinations
这个因子的选取因为也是不回头的规则，需要还是需要传入index，来标记当前用到哪个因子了
而且，要用for循环来不断加入当前因子，所以，递归的时候，并不需要 +1
这题，去重是最重要的部分。详细还是复习代码吧

320. Generalized Abbreviation
首先缩写肯定是要从第一位开始的，如果从后面开始，就miss了一些字符，肯定就错了，所以不用for循环
其次，也肯定是要记得当前走到哪个index了，所以，递归要传入index，并且index要加一
这题的重点是，abbreviate的时候如何回溯，因为毕竟当时没有加入任何东西到stringbuilder里面
所以，这个题要在加入结果后，也回溯，这样就可以把最后没走到情况二的分支也回溯

#### 递归不需要传index
46. Permutations

    Permutations 不用传index，因为上一层可以选任何数字，但后面无法用index来判断上一层用了什么
所以，要用一个boolean 数组来标记哪个元素之前用过了

47. Permutations II
    
    这个题需要注意的一点就是去重的时候，由于并没有传入index，当nums[i] == nums[i - 1]的时候，
i 和 i-1 是一样的，没有什么可以区分，这段代码会直接跳过，所以，curr的size永远满足不了
所以，需要多加一个条件，当上一个重复的元素used或者没used得时候，再skip
if (used[i] || i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) { continue; }

67. Palindrome Permutation II

    这个题，其实有点特殊，因为index有很多，for循环是循环遍历map里面符合条件的字符，这个不用传index，
因为map回溯之后，是可以回头选择之前的字符的，而且，当前字符也可能有多个，需要重复选择
但对于解，是有left 和 right 2个 `index`的，每层只把字符添加到当前的left 和 right index上。
而下层就要从left-1 和right+1 开始继续。直到left或right 出界为止    

#### 退出时扔需要回溯
320. Generalized Abbreviation

#### 无退出条件
78. Subsets, 90. Subsets II

#### 不用for loop
17. Letter Combinations of a Phone Number
93. Restore IP Addresses
320. Generalized Abbreviation


#### 总结

做提前，要问自己几个问题
1. 下一层开始的位置，是否取决于上一层结束的位置 ？（yes: 传index； no： 不传）
2. 是否可以重复使用当前元素，是否可以回头使用之前的元素
3. 所有的解是否必须以input的开头作为开头。（yes: 不用for循环； no： 用fro循环）

另外，这种题的复杂度一般都真的很复杂，比较不容易分析
但其实可以用一个简单的公式来确定 ： O(答案总数 * 构造每个答案的时间)
比如，subset 和 combination，就有2^n个解，permutation，有n!个解


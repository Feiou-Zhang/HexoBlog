---
title: 学习Python
date: 2018-03-09 13:18:06
tags: [Python]
categories: [Learn]
---
https://segmentfault.com/a/1190000000618286#xrange

### day one

##### python 里并不支持 ++i 或者 i++ 这种操作 
想increment的话，只能 i += 1 这样

##### for 循环
python 里好像并不支持在for里面加很多条件，
比如java可以 for (int i = 0; i < k && head != null; ++i) 这样
但python可以 在foreach的同时 加一个index

比如 for i, num in enumerate(nums): 
省去了一些麻烦不用总写中括号

##### return
python 并没有像java一样，在程序末尾强制要return

##### swap
python的swap真是简洁，，如果想swap list（数组）中的2个元素，只要
nums[a], nums[b] = nums[b], nums[a] 

##### xrange
新手python竟然还遇到了不同版本的问题
python3之后取消了xrange 这个函数，只保留了range
应该是range内部机制和xrange 一样了

### day two

##### 排序

简单看，好像排序有2种
1. sort() 是list专属的，是inplace 排序，return None
2. sorted() 可以排序任何类型，字典，tuple，list都可以，而且不会更改原数组，是返回一个

##### 回溯

新手python 做了个leetcode 中等题 debug了一小时可能
1. python中 string 数字 竟然不能直接当数字用？我咋记得 python 没啥type呢， 需要用int（“1”）转一下
2. 另外，递归的时候，需要在函数前面加个 self.dfs（）这样
3. string 同样是 immutable的，

##### 全局变量

普通全局变量 比如 str int 这种，如果再local 要用的话，必须在local 声明下
例如 `global num` 这样


#####  __init__

这个好像有点类似java里的构造函数，里面可以放些 类似全局变量
之后用self. 就可以直接用

##### while list

这个很有意思，while list 就是当list 不为空的时候就一直是True

##### list slices 不知道这个怎么翻译 切片吗

这里面有很多 很简洁的方法, 比如:
`list[-1]` 这样就是 get list 最后一个元素

`list[::-1]` 就是倒序一个list

`list[3:6]` 这个就是 截取list里面从第index 3 到 5 的元素

`list[3:]` 和 `list[:6]` 如果像这样只有一边有的话，那么就默认另一边是到头

`list[:-1]` 这样就是截取从第0 到 倒数第2个元素之间

##### stack

python 好像并没有stack 这么个东西，但list完全可以当stack用

peek 就是 list[-1]
pop 是有的
append 就是 push
---
title: 栈和队列
date: 2018-03-09 13:14:13
tags: [Deque]
categories: [Java基础]
---
### 优先队列

关于优先队列，以为自己算是很明白了，比如 增加新元素是 logN 时间
查看队首 肯定是 O(1)时间，如果是 找或删 某个非队首的元素，是O(n)时间

所以，就想当然以为，构建一个优先队列肯定要 O(nlogn)时间了。。。

没想到 ，大错特错了， 构建 堆的时间应该只要 O(N)而已，维基百科 很明确的写的O(N)

以下分析来源 博客 http://blog.csdn.net/don_lvsml/article/details/19546997
其实我还没怎么看懂，有兴趣的同学，可以讨论下

> 下面分析时间复杂度
n个结点的完全二叉树（堆是完全二叉树）的深度为h（根的深度为1），则h和n之间满足 2^(h-2) <n <= 2^h，h近似等于log(n)+1。

> 从代码的直观来看，每次调整一个结点需要log(n)，一共调整(n-1)/2个结点，故时间复杂度为O(nlogn)，
但要注意在中间过程中第s层的结点往下调整时实际只需最多调整（h-s）次，第s层一共调整2^(s-1)*(h-s)，这样时间复杂度可写成

### Deque, ArrayDeque, LinkedList
>> Generally, we should always use ArrayDeque instead of LinkedList
>> however, ArrayDeque does not take null value
>> 
ref: http://blog.jrwang.me/2016/java-collections-deque-arraydeque/
写的很好的一篇关于deque 源码分析的
![deque](deque.png)
因为ArrayDeque 的内部实现其实是数组，外加头尾2个指针，为了减少空间的浪费 以及扩容的开销，
聪明的java 设计者，把array当成了一个环，因为 deque无论如何只能从头尾 增删 元素，所以，元素必然是连续的，这样，把开始为占用的和已经删除的元素标为null，这样，下次新增元素的时候，就可以复用数组。 赞！
这也就是为什么 array 不能加null的原因了，因为，如果你可以加null，那么deque 就不知道，这是一个空位置可以用呢还是 user存的null呢。

#### ArrayDeque

以下几个很绕，尤其是混合使用的时候。

offer = offerLast 正常入队，加到队尾
push  = offerFirst 模拟入栈，加到栈顶
peek = peekFirst
这个peek 和 peekFirst 都是check栈顶（队首）所以既是队列的peek 也是堆栈的peek
pop = poll = pollFirst 删除并返回栈顶（队首），因为当做堆栈的时候，push把元素放到了数组的前面（栈顶），所以，pop 和 poll 这里返回的应该是同一个元素
peekLast 查看栈顶队尾（数组末端）的元素，也就是刚入队的元素
pollLast 删除并返回队尾（数组末端）的元素，也就是刚入队的元素

### Stack and Queue
Use pop and peek will throw exception if there is no elements in the stack
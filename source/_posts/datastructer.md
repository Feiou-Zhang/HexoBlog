---
title: 数据结构
date: 2018-04-10 12:33:46
tags: 
categories: [基础]
---
### 迭代器(Iterator)
什么时候更需要用迭代器来遍历？
1. 当你需要一边遍历，一边删除元素的时候，必须使用迭代器，而不能使用for循环
2. 当你需要遍历一个LinkedList的时候，用 for循环 get(index) 会超级慢，因为这个遍历时n^2复杂度
因为每次访问一个元素，都需要从头开始走到该元素
3. 当需要遍历set 和 map的时候，需要 foreach 或者迭代，因为这两个数据结构并没有index

### 优先队列

时间复杂度：
offer : O(logn)
poll : O(logn)
peek : O(1)
remove: O(n)
虽然添加元素的时间复杂度是O(logn)，但构建优先队列的时间复杂对为 O(N)

### String 和 StringBuilder

尽量避免使用 + 连字符

split的时候，如果pattern是保留字符的话，记得前面要加“//”，比如 “.” “* ” 或者 "|"
当pattern是多个的时候， 可以用 “|” 隔开

substring的时间复杂度
在老版本的java中，substring的时间复杂度是O(1), 但java7 之后变成了O(N)
原因就是，老版本中java对substring存在内存泄漏的问题，因为使用substring方法的时候，
系统内部其实还保留着对整体char[]的引用，也就是说，即使GC吃掉了原先的string，但还是有方法可以从原先的char[]来恢复原先的string
StringBuilder的insert()方法的复杂度是O(n)，因为String的底层是char[]，所以insert就涉及到了扩容的问题，所以，复杂度是O(n)级别的，
StringBuilder的reverse()方法的复杂度同样是O(n)，
所以如果要反转一个String的话，一定不要用insert(0,e)，这种复杂度会是N方级别的

for StringBuilder
the complexity of insert() method is O(n), as the String is actually a char[], so, insert() will be very similar to ArrayList add(index, e), it will need to move almost all the element in the array.

the reverse() method is also O(n).

### Tree

红黑树的特性
1. 树的节点 多了一个布尔类型遍历表示 红色或者黑色，所有的节点 非红即黑
2. root节点总是黑色
3. null节点总是黑色
4. 红色节点的两个孩子必须都是黑色节点，也就是说，不能上下相邻的两个节点同为红色
5. 从任一节点到其每个叶子的所有简单路径都包含相同数目的黑色节点。

### Map
1. put(K,V), putIfAbsent(K, V), replace(K,V), replace(K,V,V)
put就是简单的添加或覆盖，不需要match
putIfAbsent(K, V) 只有 map里不包含K，或者K 指向null的时候，才会put
replace(K,V) 需要保证K match的情况下才会更新
replace(K,V,V) 需要保证 K 和 V 同时match的情况才会更新
2. remove(K), remove(K,V) 同理

#### collisions resolution
map的原理就是存 和 取的时候，先把key hash成一个数字，然后用这个数字mod MAP的长度
来找到存储value的下标，从而实现get 为O(1)的操作
那么，如果2个不同的key hash之后mod 长度得到相同的index 该怎么办呢
解决冲撞的方式一般有2种，一种是open addressing， 另外一种是separate chaining
1. open addressing 的原理就是，如果发生冲撞，那么就为这个key选择另一个index来储存
而这个选择另一个空闲index的方法又有三种，liner/quadratic probing, double hashing
liner probing 就是依次选择下一个空闲index
quadratic 就是成倍的跳着找下一个空闲index
double hashing 是再次hash
这种方法的优点就是 1. 节省空间，2. 不许多额外的数据结构
缺点：1。 需要额外时间寻找下个index 2. 容易扎堆，cluster 3. 删除元素的时候，比较麻烦，因为要区别于从来都每被放过元素的空位，和删除元素之后的空位
2. separate chaining 是用一个单向链表，如果该index上已经有元素了，那么就以该元素为root，构建一个单链表，依次把相同index的key都放到链表里，当然list过长的时候，也会影响到hashmap的效率，所有java底层为了提高效率，设计成了当list的size大于8的时候，换成红黑树
优点： 1. 容易实现 2. 删除元素更简单 3. never fill up
缺点： 1. 浪费空间，由于load factor的原因，map会在75%满的时候就扩容，而且需要额外数据结构 2. cache 效率比open addressing 差一点

#### 遍历Map

1. 遍历 key 和 value
```java
for (Map.Entry<String, Integer> item: items.entrySet()) {
   System.out.println("k: " + item.getKey() + " V: " + item.getValue());
}
```
2. 只遍历 key 或者 value
```java
for (String key: items.keySet()) {
    System.out.println("Key = " + key);
}
for (int value: items.values()) {
    System.out.println("Value = " + value); 
}
```
3. 遍历时需要删除元素的，要用迭代器
```java
Iterator<Map.Entry<String, Integer>> item = items.entrySet().iterator();
while (item.hasNext()) {
    Map.Entry<String, Integer> curr = item.next();
    System.out.println("k: " + curr.getKey() + 
                      " V: " + curr.getValue());
    if (curr.getKey().equals("A")) {
        item.remove();
    }   
}
```

### List

1. 当需要把一个list 整体添加到另外一个list的时候，我们需要用addAll

```java
	List<Integer> list = new ArrayList<>();
	list.add(1);
	list.add(2);
	List<Integer> list2 = new ArrayList<>();
	list.add(3);
	list.add(4);
	list.addAll(list2);
```

2. list 有个专门的迭代器，叫做ListIterator，它比普通的Iterator多了几个方法
比如add(e), 添加一个新元素，会在下次调用next()的时候被返回
hasPrevious() 和 pervious() 是用来反向遍历list
nextIndex(), previousIndex() 用来返回下一个或上一个元素的index，如果没有下一个，就返回长度
如果没有上一个就返回-1
set(e)， 是在调用next() 或者 previous()之后，更改当前元素的方法
3. List 转 Array， list有个toArray的方法，可以把一维或二维的list转换成数组
```java
String[][] strs = new String[][]{1,2}{1,2};
list.toArray(strs);
```

### Array

1. asList() 这个方便并不适用8中原始数据类型，而且，用asList()得到的list并没有add()和remove()这两个方法， 因为asList() 返回的是一个 `fixed-size` 的list
2. 二维数组的asList()
```java
String[][] strs = new String[][]{1,2}{1,2};
List<List<String>> = (List) Arrays.asList(strs);
```
3. 关于复制数组，尽量使用System.arraycopy(source, index, destination, index, length)
4. 二维的数组，在声明的时候，可以分别初始每一个一维数组的长度
```java
int[][] matrix = new int[3][];
matrix[0] = new int[2];
matrix[1] = new int[4];
matrix[2] = new int[3];
```

### Deque, Stack, Queue

ArrayDeque不能添加null 元素，因为ArrayDeque 的内部实现其实是数组，外加头尾2个指针，为了减少空间的浪费 以及扩容的开销，聪明的java 设计者，把array当成了一个环，因为 deque无论如何只能从头尾 增删 元素，所以，元素必然是连续的，这样，把开始为占用的和已经删除的元素标为null，这样，下次新增元素的时候，就可以复用数组。 赞！
这也就是为什么 array 不能加null的原因了，因为，如果你可以加null，那么deque 就不知道，这是一个空位置可以用呢还是 user存的null呢。

ref: http://blog.jrwang.me/2016/java-collections-deque-arraydeque/

以下几个很绕，尤其是混合使用的时候。

offer = offerLast 正常入队，加到队尾
push = offerFirst 模拟入栈，加到栈顶
peek = peekFirst
这个peek 和 peekFirst 都是check栈顶（队首）所以既是队列的peek 也是堆栈的peek
pop = poll = pollFirst 删除并返回栈顶（队首），因为当做堆栈的时候，push把元素放到了数组的前面（栈顶），所以，pop 和 poll 这里返回的应该是同一个元素
peekLast 查看栈顶队尾（数组末端）的元素，也就是刚入队的元素
pollLast 删除并返回队尾（数组末端）的元素，也就是刚入队的元素




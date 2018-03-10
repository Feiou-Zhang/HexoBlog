---
title: List
date: 2018-03-09 13:38:32
tags: [list]
categories: [数据结构]
---

### When to use LinkedList over ArrayList?

ArrayList with ArrayDeque are preferable in much more use-cases than LinkedList. Not sure — just start with ArrayList.

LinkedList and ArrayList are two different implementations of the List interface. LinkedList implements it with a doubly-linked list. ArrayList implements it with a dynamically re-sizing array.

As with standard linked list and array operations, the various methods will have different algorithmic runtimes.

For LinkedList<E>

get(int index) is O(n/4) average
add(E element) is O(1)
add(int index, E element) is O(n/4) average
     but O(1) when index = 0 <--- main benefit of LinkedList<E>
remove(int index) is O(n/4) average
Iterator.remove() is O(1) <--- main benefit of LinkedList<E>
ListIterator.add(E element) is O(1) <--- main benefit of LinkedList<E>
Note: O(n/4) is average, O(1) best case (e.g. index = 0), O(n/2) worst case (middle of list)

For ArrayList<E>

get(int index) is O(1) <--- main benefit of ArrayList<E>
add(E element) is O(1) amortized, but O(n) worst-case since the array must be resized and copied
add(int index, E element) is O(n/2) average
remove(int index) is O(n/2) average
Iterator.remove() is O(n/2) average
ListIterator.add(E element) is O(n/2) average
Note: O(n/2) is average, O(1) best case (end of list), O(n) worst case (start of list)

LinkedList<E> allows for constant-time insertions or removals using iterators, but only sequential access of elements. In other words, you can walk the list forwards or backwards, but finding a position in the list takes time proportional to the size of the list. Javadoc says "operations that index into the list will traverse the list from the beginning or the end, whichever is closer", so those methods are O(n/4) on average, though O(1) for index = 0.

ArrayList<E>, on the other hand, allow fast random read access, so you can grab any element in constant time. But adding or removing from anywhere but the end requires shifting all the latter elements over, either to make an opening or fill the gap. Also, if you add more elements than the capacity of the underlying array, a new array (1.5 times the size) is allocated, and the old array is copied to the new one, so adding to an ArrayList is O(n) in the worst case but constant on average.

So depending on the operations you intend to do, you should choose the implementations accordingly. Iterating over either kind of List is practically equally cheap. (Iterating over an ArrayList is technically faster, but unless you're doing something really performance-sensitive, you shouldn't worry about this -- they're both constants.)

The main benefits of using a LinkedList arise when you re-use existing iterators to insert and remove elements. These operations can then be done in O(1) by changing the list locally only. In an array list, the remainder of the array needs to be moved (i.e. copied). On the other side, seeking in a LinkedList means following the links in O(n/2) for worst case, whereas in an ArrayList the desired position can be computed mathematically and accessed in O(1).

Another benefit of using a LinkedList arise when you add or remove from the head of the list, since those operations are O(1), while they are O(n) for ArrayList. Note that ArrayDeque may be a good alternative to LinkedList for adding and removing from the head, but it is not a List.

Also, if you have large lists, keep in mind that memory usage is also different. Each element of a LinkedList has more overhead since pointers to the next and previous elements are also stored. ArrayLists don't have this overhead. However, ArrayLists take up as much memory as is allocated for the capacity, regardless of whether elements have actually been added.

The default initial capacity of an ArrayList is pretty small (10 from Java 1.4 - 1.8). But since the underlying implementation is an array, the array must be resized if you add a lot of elements. To avoid the high cost of resizing when you know you're going to add a lot of elements, construct the ArrayList with a higher initial capacity.
[from stackoverflow](https://stackoverflow.com/questions/322715/when-to-use-linkedlist-over-arraylist)


### List  - add  addAll

when need add list to a list, we must use addAll instead of add
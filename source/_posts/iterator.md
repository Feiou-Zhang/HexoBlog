---
title: Iterator
date: 2017-03-13 22:15:47
tags: [Iterator]
categories: [Java基础]
---
### Why and when should use the Iterator?
`Important! DO NOT use for loop to traversal a LinkedList`
​
#### What is the iterator for?
​
It used to cycle through the elements in a collection, in other words, we can consider the iterator as the traversal simply. By using the iterator, you can obtain or remove elements. (ListIterator extends Iterator to allow bidirectional traversal of a list).
```java
boolean hasNext( )
​
//Returns true if there are more elements. Otherwise, returns false.
    
Object next( )
​
//Returns the next element. Throws NoSuchElementException if there is not a next element.
    
void remove( )
​
//Removes the current element. 
//Throws IllegalStateException if an attempt is made to call remove( ) that is not preceded by a call to next( ).
```
Every data structure or algorithm is designed for solve some problem. So, before we have iterator, how do we traversal.
```java
int[] arrays = new int[5];
for(int i=0; i<arrays.length; i++)
    print(arrays[i]);
```
> This main defect of this method is that we have to know the data structure of the collection, also, if you want to change to another type of collection, the code won't be useable.
​
The iterator perfectly solved this problem, you do not have to know much about the object, and no matter what kind of data structure you want to traversal(`List, Set, or Tree`), iterator always use one logic to traversal.
​
<br/>
​
So, in what kind of situation, we should consider using interator in the first place? or in other words, in which way, the iterator is better than the get(index)?
​
<br/>
​
Iterator is very useful, but it does not mean you should always use it when you want to do the traversal without thinking. Actually, if the for loop or enhance for loop can easily solve the problem, there is no need to use the iterator, they are about the same. `However, in some situation, you better to use iterator instead.`
​
<br/>
​
* When you need to remove an item as you scanning. 
    * By using the enhanced for loop, if you remove an element during the traversal, it will throw the ConcurrentModificationException.
    * By using the regular for loop, it will delete the element without any exception, however, there will be a chance that you miss to delete others, for example, after you delete the second element then visit the third element, however, as it just deleted the second element, the third element you visited is actually the fourth element, in this case, if the third element is also the element you want to delete, then you will miss that one.
​
*delete from the end to front also works*
```java
String[] strs = new String[]{"aa","ab","ab","ac","cd"};
        List<String> ls = new ArrayList<>();
        for(String str: strs)
            ls.add(str);
        System.out.println(ls);  //[aa, ab, ab, ac, cd]
        String s = "ab";
        for(int j = 0; j < ls.size(); ++j)
            if(ls.get(j).equals(s))
                ls.remove(j);
        System.out.println(ls);  //[aa, ab, ac, cd]
        for(int j = ls.size()-1; j >= 0; --j)
            if(ls.get(j).equals(s))
                ls.remove(j);
        System.out.println(ls);  //[aa, ac, cd]
```
* When you need to traverse a LinkedList
    * Technically you can still use the for loop, it will give you the right result without any exception. However, it will be `extremely slow`!
    * Why? Because every time get(index), it will traverse all the elements before that index.
    * Actually the enhanced for loop is also an option, however, an enhanced for loop is an iterator indeed.
* when you need to traverse a Set
    * Because there is no get method for Set, it has to use the iterator to traverse.

### Iterator and ListIterator
* First of all, Iterator can be used in any kind of collections, such as List, Set, Tree. However, ListIterator can only be used in List and Vector.
* ListIterator has all the method that Iterator does, and a few more.
    * void add(Object obj) // Inserts obj into the list in front of the element that will be returned by the next call to next( ).
    * boolean hasPrevious( ) and Object previous( ) // Those two allow you can do the reverse traversal.
    * nextIndex() and previousIndex() // Returns the index of the next or previous element. if there is no next element, it will returns the size of the list, and if there is no previous element, it will returns -1.
    * void set(Object obj) // Assigns obj to the current element. This is the element last returned by a call to either next( ) or previous( ).

```java
List<String> ls = new LinkedList<>();
ls.add("aa");
ls.add("ab");
ls.add("ac");
ListIterator it = ls.listIterator();
while(it.hasNext()){
    if(it.next().equals("aa")) it.add("av");
    System.out.print(it.next() + "" + it.nextIndex());
    if(it.next().equals("ac")) it.set("ae");
}
System.out.println(ls); //ab3[aa, av, ab, ae]
 ```
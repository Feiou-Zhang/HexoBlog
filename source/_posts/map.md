---
title: 基础 - HashMap
date: 2018-02-27 01:55:08
tags: [HashMap]
categories: [Java基础]
---

### Map

#### HashMap collisions resolution

##### open addressing 

it means if the collision happened, find another open slot by using
1. liner probing
2. quadratic probing
3. double hashing

###### advantage
1. better cache perpormace as everything is stored in the same table
2. no waste on space

###### disadvantage
1. need more time to finding the next open slot
2. more operating when deleting elements
3. clustering

##### Separate Chaining

This is the way using by Java.

In java, a HashMap is actually using array and each cell of the array point to a single linked list, whenever there is a collision, the element will add the tail of the linkedList, the default initial size is `16`, the default load factor is `0.75`, this means, when the numbers of elements stored in the hashmap hit the size times load factor(16 * 0.75), java will double the size. 

`Since Java 8, if the elements stored in the linked reached 8, java will switch the linked list to red-black Binary Search Tree.`

###### advantage
1. easy to implement
2. easy to delete element compare to open addressing
3. never fill up

###### disadvantage
1. used addtional data structure made its cache performance not as good as using open addressing
2. there are always many spaces are never used because of the load factor
3. and need extra space for the pointers(links)

#### Traversal a map

1 traversal both key and value

```java
for (Map.Entry<String, Integer> item: items.entrySet()) {
   System.out.println("k: " + item.getKey() + " V: " + item.getValue());
}
        
//java 8
items.forEach((k, v) -> System.out.println("k: " + k + " V: " + v));
items.forEach((k, v) -> {if (k.equals("C")) System.out.print(v);});
```
2 traversal only keys or values

```java
for (String key: items.keySet()) {
    System.out.println("Key = " + key);
}
for (int value: items.values()) {
    System.out.println("Value = " + value); 
}
```
##### iterator (when need to remove entry during traversal)

for the remove() function, can be only used after calling the next() function, 
and each next() good for one remove()

1. both key and value
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
2. for only key or value
```java
Iterator<String> keys = items.keySet().iterator();
while (keys.hasNext()) {
    String currKey = keys.next();
    System.out.println("k: " + currKey);
    if (currKey.equals("A")) {
        keys.remove();
    }   
}
```

bad way to traversal a map
```java
Map<Integer, Integer> map = new HashMap<Integer, Integer>();  
  
for (Integer key : map.keySet()) {  
  
    Integer value = map.get(key);  
  
    System.out.println("Key = " + key + ", Value = " + value);
}  
```
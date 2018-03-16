---
title: array
date: 2017-03-13 22:18:16
tags: [Array]
categories: [Java基础]
---
### 2D array
when initializing a 2D array, it is optional to give the size of the 
"column"
```java
//first way
int[][] matrix = new int[3][3];
//second way
int[][] matrix = new int[3][];
matrix[0] = new int[2];
matrix[1] = new int[4];
matrix[2] = new int[3];
```
#### 2D array asList `very important`
```java
//we all know a 1D array can be easily convert to a list
String[] strs = new String[]{1,2};
List<String> ls = Arrays.asList(strs);

//actually we can also convert a 2D array to a 2D list
String[][] strs = new String[][]{1,2}{1,2};
List<List<String>> = (List) Arrays.asList(strs);

```

### Characteristic:
* There will be only one type of date in an array. Strongly Typed.
* The size of the array is not changeable. In other words, fixed size.
* Array is one kind of object.
#### Advantages:

* Fast. Access to the elements by using the index that provided by arrays.
* Can be easily implemented with other data structures.
* Can store many elements at a time(minor).
#### Disadvantages:

You must know the size when you create an array.
As the size is fixed, it will be very difficult to add or remove elements from an array.
> After understanding the characteristics of array, you will be able to determine when should you choose array. And you will bave an efficient data structure if all those three items satisfied.

* You know the size
* There will be only one type.
* You do not need to add or remove any elements.

#### Some common usage
##### Declare an array
```java
int[] array1 = new int[3];
int[] array2 = {1,3,5};
int[] array3 = new int[]{1,3,5};
```
##### Print out an array
```java
int[] array1 = {1,3,7};
System.out.println(array1); //this will only print out the address
String str = Arrays.toString(array1);
System.out.print(str); 
```
##### **Copy an array**
```java
//this will be the most efficient way to copy an array
System.arraycopy(src, startindex, dest, startindex, length);
//following two actually using the system arraycopy method, so will less efficient
Arrays.copyof(src, length);
Arrays.copyofRange(src, startindex, endindex);
//"=" this will only copy the address to the new reference
b=a;
```
##### Convert an array to a list 
```java
String[] array1 = new String[]{"1","3","7"};
ArrayList<String> list = new ArrayList<String>(Arrays.asList(array1));
System.out.print(list);
```
##### Something need to be careful `asList`
* The asList will NOT work for any of the 8 primitive type arrays.
* Even for an object, by simply using the Array.asList() will not have the add() or remove() methods, because it returns a **fixed-size** list backed by the specified array.
```java
//two ways to use the asList()
1. List<String> ls = Arrays.asList(new String[]{"a","b","c"});
2. List<String> ls = Arrays.asList("a","b","c");

String[] strs = new String[]{"a","b","c"};
List<String> ls = Arrays.asList(strs);
System.out.println(ls); //[a, b, c]
System.out.println(ls.size());//3
System.out.println(ls.contains("a"));//true
System.out.println(ls.get(1));//b
ls.set(0,"d");
System.out.println(ls);//[d, b, c]
ls.add("e");//Error:
ls.remove("b");//Error:
```


---
title: Some Thoughts
date: 2018-03-09 13:16:26
tags: [Thoughts]
categories: [Java基础]
---

#### singletonList
做n皇后 写corber case的时候 写了一个

`if (n == 1) return res.add(new ArrayList<>(Arrays.asList("Q"));` 感觉没什么问题呢，
竟然被 IDEA 提示，说如果list里面只放一个元素，这样很不效率，提示我用Collections.singletonList(), 
这东西还真是第一次见，遂查看了下 api解释。然后我就在想。。java 干嘛要设计这么一个specific的东西啊，
这东西有几个人会用的到啊，，即使用的到，，有几个人能知道这东西的存在啊~！

#### 不过IDEA 到时确实挺厉害，最近确实也提示了不少东西，

比如，不要手动用 for loop 去deep copy 一个数组，果断用System.arraycopy()

还有用StringBuilder.append() 的时候，我里面放了一个 str + str
被提示，用2次append() 比较效率

#### time complexity of substring, reverse, insert

in the older version of java, it was O(1), but after java7, it became to a O(n) time complexity.
​
The old design may cause leak of memory, because when using substring method, it keeps the reference to the whole original char[] value, that means, even after the GC eat the original String, we can still recover the string from the char[] value.
​
`for StringBuilder`
​
the complexity of insert() method is O(n), as the String is actually a char[], so, insert() will be very similar to ArrayList add(index, e), it will need to move almost all the element in the array.
​
the reverse() method is also O(n).
​
`interesting point`
​
The thing is, if you try to reverse a String, there are two approaches.
​
1. use reverse() method
2. keep insert(0, e) to the front. but this approach will be a O(n * n) approach

#### >> and >>>

the different between >> and >>>
##### 1 it will be same when dealing with positive numbers
15 >> 2 = 3
15 >>> 2 = 3

0000 1111 >> 2 = 00000011 = 3
0000 1111 >>> 2 = 00000011 = 3
##### 2. but not same for nagative numbers

">>"  use 1 to fill up the left empty position
">>>"  use 0, then will turn the nagative to postive 

-15 >> 2 = -4
-15 >>> 2 = 60

for the number 15 (inverse code plus 1 is complement)
binary code: 00001111
inverse code (one's complement) : 11110000
Complement code (two's complement): 11110001

11110001 >> 2 = 11111100
11110001 >>> 2 = 00111100

#### int, long, double, char, boolean

i byte = 8 bits

int : 4 bytes (32 bits) -2147483648 ~ 2147483647
long: 8 bytes (64 bits) 
char: 2 bytes (16 bits)
boolean: 1 byte (8 bits)

> Assigning a value of one type to a variable of another type is known as `Type Casting`.

##### Primitive type
will based on the bits size of the type
byte 8, char 16, short 16, int 32, long 64, float 32, double 32, boolean 1(no casting to other)

##### Widening Casting(Implicit)
if casting from a smaller bit size type to a larger bit size, java will automatically do the type casting. 
```java
int a = 1;
double b = a;
```

##### Narrowing Casting(Explicitly)
otherwise, must explicit the type as the following example.
```java
double c = 2.0;
int d = (int)c;
```
`There are two static methods in the Math class,  pow, and sqrt, they both only take the double type and return double type.`

##### Object

Similar to the primitive type

when casting from superclass to subclass, no explicit type casting required.
```java
String s = "hello";
Obeject obj = s;
```
otherwise, 
```java
Obeject obj = "hello";
String  s = (String)obj;
```
##### a=a+b VS a+=b

the difference between a=a+b and a+=b is, when you do a+=b, java will automatic cast the data type to the target datatype.

for the efficiency aspect, they are totally the same, even a=a+b seems used one more temporary variable, however, java will automatic optimize a=a+b to a+=b during the runtime.

#### String split
when split with “.” “*” or "|", must add "//" before

when need split with more than one thing, should use | in between
like "and|or"


#### How to swap two numbers without using a temporary variable
int x = 5, y = 10;

three ways can do this

1. 
x = x + y;
y = x - y;
x = x - y;

2. 
x = x * y;
y = x / y;
x = x / y;

3. 
x = x ^ y;
y = x ^ y;
x = x ^ y;

for the approachs 1 and 2,

if the numbers are very large, x + y or x * y will cause overflow issues.

for all the approachs 1, 2 and 3，you can not swap x and x.
(not the case the value of the x and y are same)
x = 5;

x = x + x; //10
x = x - x; //0
x = x - x; //0
---
title: gcd
date: 2018-03-09 13:32:58
tags:
categories:
---
![gcd](gcd.jpg)

### Greatest Common Divisor
code first
​
```java
public int gcd(int a, int b) {
    if (a == 0) {
        return b
    }
    return gcd(b % a, a);
}
​
```
we assume n is the common divisor for 1112 and 695
but we do not know the value of n
then we let 1112 = an and 695 = bn,
if (an - bn) and bn keep subtract each other, eventually n will be left (unless a == b)
​
the gcd of an and bn is the same as (a-b)n and bn
​
this way, we simple transfer a problem to a smaller problem.
​
same idea, a % b is relevant to a subtract n 6s, speed up the process
return gcd(b % a, a) is used to make sure the smaller number is always in the front.
​
### Lowest Common Multiple
​
lcm = a * b / gcd(b % a, a)
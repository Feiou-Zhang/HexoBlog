### throws and throw

throws 是在方法头中 声明的
throw  是在方法里用的

throws 可以catch1个或多个例外
throw 只可以catch 一种例外

throws 表面的是一种可能，这个方法里可能有例外，也可能没
throw 用于此时这里肯定有一个例外

throws 可以单独使用
throw 需要配合try-catch-finally

当程序运行了throw语句，程序就会停止
ref：https://blog.csdn.net/ronawilliam/article/details/3299676


###  >> 和 >>>

首先，计算机中，负数是以补码的形式储存的。
反码就是原码取反，补码就是反码加1
"<<"      :     左移运算符，num << 1, 相当于num乘以2

">>"      :     右移运算符，num >> 1, 相当于num除以2

">>>"     :     无符号右移，忽略符号位，空位都以0补齐

1. 如果是处理正数，那么 >> 和 >>> 是一样的作用

2. 如果是处理负数
	>> 用1来填充空位
	>>> 用0，这样，就会把负数变成正数

	举例说明就是
	-15 >> 2 = -4
  	-15 >>> 2 = 60
  	对于数字15，
  	原码：00001111
  	反码：11110000
  	补码：11110001

  	11110001 >> 2 = 11111100
	11110001 >>> 2 = 00111100

### Casting

i byte = 8 bits
int : 4 bytes (32 bits) -2147483648 ~ 2147483647
long: 8 bytes (64 bits)
char: 2 bytes (16 bits)
boolean: 1 byte (8 bits) -- 布尔型是不能转换成其他任何数据类型的

Java的数据转型 如果是从小的数据类型向上转型，是不用特别声明的
比如

```java
int a = 1;
double b = a;
```
但如果是从大的数据类型向下转型，是需要 明确声明的， 比如
```java
double c = 2.0;
int d = (int)c;
```

同样的，对于对象的类型转换，超类和子类的互相转换也同样适用上面的规则，比如
```java
String s = "hello";
Obeject obj = s;

Obeject obj = "hello";
String  s = (String)obj;
```


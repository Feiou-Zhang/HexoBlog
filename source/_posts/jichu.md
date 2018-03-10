---
title: Java基础
date: 2018-03-09 13:46:50
tags: 
categories: 
---
### throws and throw
throws is using in the the method header
throw is using inside of the method

throws: the method throws the exception, so the one who calles the method will deal with it.
throw: a statement throws the exception, so the current method will deal with it.

throws can catch one or more exceptions
throw can catch only one kind of exception

throws is actually meanning there is a possbile there could be a exception
throw is using when there is an actual exception

you can use throws by itself
but you have to use throw with try-catch-finally or throw with throws together.

the program will stop after executing the throw statement.

http://blog.csdn.net/ronawilliam/article/details/3299676

### autoboxing andunboxing

java has 8 primitive data types
and there are 8 number classes for each primitive data type 

the reason why we need the number classes
1. the collections only hold classes
2. there are many static or non-static method for the number classes, like, Integer.MAX_VALUE, we do not need to memorize the value,

### Comparable and Comparato

```java
class Solution {
    class Node implements Comparable{
        int val;
        int index;
        Node(int val, int index) {
            this.val = val;
            this.index = index;
        }
        public int compareTo(Object node) {
            if (node == null) {
                return -1;
            }
            Node n = (Node)node;
            return this.val - n.val;
        }
    }
    public int minCostII(int[][] costs) {
        if (costs == null || costs.length == 0 || costs[0] == null || costs[0].length == 0) {
            return 0;
        }
        PriorityQueue<Node> lastCosts = new PriorityQueue<>();
        // PriorityQueue<Node> lastCosts = new PriorityQueue<>(new Comparator<Node>() {
        //     @Override
        //     public int compare(Node n1, Node n2) {
        //         return n1.val - n2.val;
        //     }
        // });
        for (int i = 0; i < costs[0].length; ++i) {
            lastCosts.offer(new Node(costs[0][i], i));
        }
        for (int i = 1; i <  costs.length; ++i) {
            PriorityQueue<Node> currCosts = new PriorityQueue<>();
            // PriorityQueue<Node> currCosts = new PriorityQueue<>(new Comparator<Node>() {
            //     @Override
            //     public int compare(Node n1, Node n2) {
            //         return n1.val - n2.val;
            //     }
            // });
            int lastMinIndex = lastCosts.peek().index;
            for (int j = 0; j < costs[0].length; ++j) {
                if (j == lastMinIndex) {
                    Node tmp = lastCosts.poll();
                    currCosts.offer(new Node(costs[i][j] + lastCosts.peek().val, j));
                    lastCosts.offer(tmp);
                } else {
                    currCosts.offer(new Node(costs[i][j] + lastCosts.peek().val, j));
                }
            }
            lastCosts = currCosts;
        }
        return lastCosts.peek().val;
    }
}
```
> Since Java 8, we can create a PriorityQueue with Comparator and `without` an inttial capacity！ yea~

#### normal case
```java
int[] nums = new int[]{3,1,7,4,5,2,8,6};
Arrays.sort(nums);
System.out.print(Arrays.toString(nums));
List<Integer> ls = new ArrayList<>();
ls.add(3);
ls.add(1);
ls.add(4);
ls.add(2);
Collections.sort(ls);
System.out.print(ls);
```
What if we want to sort some people by their height?

```java
public class Person{
    int weight，height;
    Public Person(int weight, int height){
        this.weight = weight;
        this.height = height;
    }
    public String toString(){
        return name + " " + height;
    }    
}

Person[] p = new Person[]{new Person(65,177), new Person(64,178};
Arrays.sort(p);
```
Of course, it will not sort the person, because the system does not know what to sort.

In order to use the Arrays.sort or Collections.sort on any thing that are not natural sorting, we have to implement comparable to that class.

```java
public class Person implements Comparable<Person>{
    ...
    public int compareTo(Person person){
        if(this.height < person.height)
            return -1;
        else if(this.height > person.height)
                return 1;
        else
            return 0;    
    }
    ...
}
```
This seems easy to implement, however, what if we are not able to access or change any source code.
Then we will need to use `Comparator`
```java
public class personComparator implements Comparator<Person>{
    @override
    public int compare(Person p1, Person p2){
        if(p1.height < p2.height)
            return -1;
        else if(p1.height > p2.height)
                return 1;
        else
            return 0;
    }
}
```
Then base on the method, you can do any comparison you want.
*For exampel*
```java
//a static method of Arrays
sort(T[] a, Comparator<? super T> c)
sort(person, new personComparator());
//a static method of Collections
sort(List<T> list, Comparator<? super T> c)
//to create a PriorityQueue
PriorityQueue(int initialCapacity, Comparator<? super E> comparator)

//to create a TreeSet or TreeMap
TreeSet(Comparator<? super E> comparator)
TreeMap(Comparator<? super E> comparator)
```

follow up
In Java 8, there is a new method called spliterator()...


##### something need to know about Abstraction
* if there is at lease one abstract method, the class has to be abstract.
* but an abstract class may or may not contain abstract methods.
* when a non-abstract extends an abstract class, the subclass must override all of the abstract methods.
* when a abstract extends an abstract class, there is no need to override all of the abstract methods, even can not have the same abstract method.
* abstract and final can not describe a class at the same time obviously, abstract can not destribe a method with private, static, and final at the same time.
* because as an abstract class or method, there is no real meaning by it self, it has to be extended or override, the final or static just works in the oppsite way.

##### There are some main differences between abstract class and interface

1. of course the syntax will be different
```java
public abstract class Animal{
    abstract void eat();
    void sleep(){
        ...
    }
}

interface move{
    void crawl();
    void fly();
}
```
a subclass can extends at most one abstract class but can implements many interfaces
2. from the design level
Generally, an abstract class abstract all the common objects, however, the interface abtracts all the common functionalities or behaviors cross different objects 

##### Why do we need abstract class/method

This is a question I do not understand well for a while.
If you ask Google, many people will tell you the following:
> if you have an Animal class, and a sound method in it, as different animals may sound different, it is better to implement it in each subclass. Also, logically, there is no specific sound for an abstract animal, so we can make the sound method abstract which is a empty method.

This sounds reasonable, however, it seems answered a different question which is why a abstract method should be empty. As we will need to create the method in each subclass, the empty method in the abstract class seems redundant and useless.

In my opinion, there are two main reasons that we need abstract class/method
1. As when a non-abstract extends an abstract class, the subclass must override all of the abstract methods, to have abstract method is forcing the subclass to override those methods
2. To keep the empty abstract methods in the superclass just like to have a table of content in a book, it gives other developers an idea what does this class has and also remind other developers to override those methods.


#### Encapsulation
> encapsulation is never designed to hide the `data`
 
1. encapsulation allows you to encapsulate things that should not be modified by other parts of your program.

2. encapsulation hides unnecessary/complex detaisl from the other, makes objects easier to use and understand.

##### about the setter method
1. use setter only if it necessary
2. setter can be declared as protected if necessary
3. setter can be used for validation or limit the values that can be stored in a field
4. provide thread satety by synchronizing the method


> 1. 暴露了太多使用者不需要知道的细节，增加心智负担
> 这一条很好理解，就问你是喜欢看10行的接口定义，还是喜欢看100行的实现。
> 2. 修改时很容易破坏兼容性
> 比如某个暴露出来的属性仅仅是内部使用，但是外部使用者也使用了它，那么当他变化时（比如你觉得名字不好，换个名字，或者觉得没用，去掉了这个属性）那么当你更新这个类的时候，使用者那边就要出现问题了。有了接口以后，尽可能保证接口不变，即使真的要移除某个属性（比如变成计算属性），也可以修改相应的访问器来解决。
> 3. 安全性不足
> 这里的安全性当然不是指恶意使用者的恶意修改（这种情况下，用private和public根本没用），而是指正常使用下从外部意外的修改了内部状态或者使用了不合法的值的所造成的一系列问题，因为属性没办法监控修改路径，也不能前置检查输入的合法性——想想看，如果有这个需求时，你能想到的方法是不是就是加一个设置函数？

#### Polymorphism

1. static(complie time)
    method overloading: this allows to have more than one method having the same name but with different parameters in numbers, sequence and data type.
    `Method return type doesn’t matter in case of overloading`
    
In java API, there are many examples, like substring, you can do substring(int startindex, int endindex), or you can also do substring(int startindex)
Also, sometimes when we use helper function to solve some problem recursionly, we can also use static polymorphism.

2. dynamic(run time)
    method overriding: this allows the subclasses have the excactlly same method name and parameters to override the method in the superclass
3. polymorphism also uesd as a super class reference refer to a subclass object
```java
List<Integer> list = new ArrayList<>();
or
Set<Integer> ts = new TreeSet<>();
```
`if ues a super class reference refer to a subclass object, only the methods in both superclass and subclass can be used.`
for example
```java
// the void method trimToSize() is only in ArrayList but not in List
List<Integer> list = new ArrayList<>();
list.trimToSize(); // will get a error.
```
#### inheritance and composition - `Code reuse`

the rule: 
when two classes have a `IS-A` relationship, then use inheritance
`otherwise` use composition


1. the change of superclass will affect to all the subclass, too many inheritance will make the project more difficult to maintain


Summary

* An object is an instance of a class.
* Encapsulation provides the security that keeps data and methods safe from inadvertent changes.
* Inheritance is a parent-child relationship of a class which is mainly used for code reusability.
* Polymorphism definition is that Poly means many and morphos means forms.
* Using abstraction one can simulate real world objects.
* Abstraction provides advantage of code reuse
* Abstraction enables program open for extension

##### something need to know about Abstraction
* if there is at lease one abstract method, the class has to be abstract.
* but an abstract class may or may not contain abstract methods.
* when a non-abstract extends an abstract class, the subclass must override all of the abstract methods.
* when a abstract extends an abstract class, there is no need to override all of the abstract methods, even can not have the same abstract method.
* abstract and final can not describe a class at the same time obviously, abstract can not destribe a method with private, static, and final at the same time.
* because as an abstract class or method, there is no real meaning by it self, it has to be extended or override, the final or static just works in the oppsite way.

##### There are some main differences between abstract class and interface

1. of course the syntax will be different
```java
public abstract class Animal{
    abstract void eat();
    void sleep(){
        ...
    }
}

interface move{
    void crawl();
    void fly();
}
```
a subclass can extends at most one abstract class but can implements many interfaces
2. from the design level
Generally, an abstract class abstract all the common objects, however, the interface abtracts all the common functionalities or behaviors cross different objects 

##### Why do we need abstract class/method

This is a question I do not understand well for a while.
If you ask Google, many people will tell you the following:
> if you have an Animal class, and a sound method in it, as different animals may sound different, it is better to implement it in each subclass. Also, logically, there is no specific sound for an abstract animal, so we can make the sound method abstract which is a empty method.

This sounds reasonable, however, it seems answered a different question which is why a abstract method should be empty. As we will need to create the method in each subclass, the empty method in the abstract class seems redundant and useless.

In my opinion, there are two main reasons that we need abstract class/method
1. As when a non-abstract extends an abstract class, the subclass must override all of the abstract methods, to have abstract method is forcing the subclass to override those methods
2. To keep the empty abstract methods in the superclass just like to have a table of content in a book, it gives other developers an idea what does this class has and also remind other developers to override those methods.

### Data Segment
为什么String=String？

在JVM中有一块区域叫做常量池，关于常量池，我在写虚拟机的时候有专门提到

常量池中的数据是那些在编译期间被确定，并被保存在已编译的.class文件中的一些数据。除了包含所有的8种基本数据类型（char、byte、short、int、long、float、double、boolean）外，还有String及其数组的常量值，另外还有一些以文本形式出现的符号引用。

Java栈的特点是存取速度快（比堆块），但是空间小，数据生命周期固定，只能生存到方法结束。我们定义的boolean b = true、char c = 'c'、String str = “123”，这些语句，我们拆分为几部分来看：

1、true、c、123，这些等号右边的指的是编译期间可以被确定的内容，都被维护在常量池中

2、b、c、str这些等号左边第一个出现的指的是一个引用，引用的内容是等号右边数据在常量池中的地址

3、boolean、char、String这些是引用的类型

栈有一个特点，就是数据共享。回到我们第一个例子，第五行String str0 = "123"，编译的时候，在常量池中创建了一个常量"123"，然后走第六行String str1 = "123"，先去常量池中找有没有这个"123"，发现有，str1也指向常量池中的"123"，所以第七行的str0 == str1返回的是true，因为str0和str1指向的都是常量池中的"123"这个字符串的地址。当然如果String str1 = "234"，就又不一样了，因为常量池中没有"234"，所以会在常量池中创建一个"234"，然后str1代表的是这个"234"的地址。分析了String，其实其他基本数据类型也都是一样的：先看常量池中有没有要创建的数据，有就返回数据的地址，没有就创建一个。

第二个例子呢？Java虚拟机的解释器每遇到一个new关键字，都会在堆内存中开辟一块内存来存放一个String对象，所以str2、str3指向的堆内存中虽然存储的是相等的"234"，但是由于是两块不同的堆内存，因此str2 == str3返回的仍然是false，网上找到一张图表示一下这个概念：
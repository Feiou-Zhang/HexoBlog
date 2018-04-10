---
title: 面向对象
date: 2018-04-10 12:31:13
tags: [OO]
categories: [基础]
---
### Java - Object Oriented

#### Abstraction
1. 抽象的特性
	1. 一个类，哪怕只有一个抽象方法，那么这个类也必须是个抽象类， 但一个抽象类，是可以不包含任何抽象方法的
	2. 当一个非抽象类 继承一个抽象类的时候，子类必须重写这个抽象类的所有抽象方法，抽象类继承抽象类则不用
	3. abstract 和 final不能同时形容一个类，因为abstract必须要被继承才能用，而final是不让别人继承
	4. abstract 也不能喝 private, static, final 来同时形容一个方法
2. 为什么需要抽象
	1. 抽象类像是一个蓝图，它规定了一些必须有的方法，并且强制继承它的子类要重写这些方法
	2. 也像一个目录

#### Encapsulation

1. 封装 允许你封装一些你不希望这个程序的其他部分触碰的东西
2. 封装 隐藏了一些不必要，过于复杂的 细节
3. 解耦设计，只暴露必要的方法，接口，有利于程序之后的修改

关于setter 和 getter
	1. 他们不一定非要成对出现，反而，setter只需要在必要的时候才用
	2. setter 可以用来 验证 和 限制 一些 赋值

#### Polymorphism

1. static(complie time)
method overloading: 允许你有相同的方法名，接受不同的参数，不同可以是 数量的，顺序的，类型的
但返回类型 是不在这个不同的范围内的
2. dynamic(run time)
method overriding: 允许你有相同的方法名以及相同的参数，用来覆盖父类的方法

#### inheritance and composition

当两个类 有 IS-A 的关系的时候，可以使用继承，否则应当使用 composition
因为继承是一种强耦合，超类的修改会影响所有子类，而且一个子类只能继承一个父类
但一个子类可以有多个接口

#### 总结
1. An object is an instance of a class.
2. Encapsulation provides the security that keeps data and methods safe from inadvertent changes.
3. Inheritance is a parent-child relationship of a class which is mainly used for code reusability.
4. Polymorphism definition is that Poly means many and morphos means forms.
5. Using abstraction one can simulate real world objects.
6. Abstraction provides advantage of code reuse
7. Abstraction enables program open for extension
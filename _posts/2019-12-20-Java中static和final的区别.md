---
layout:     post
title:      Java中static和final的区别
subtitle:   2019-12-20-Java中static和final的区别
date:       2019-12-20
author:     KASON
header-img: img/articleBg/java_bg.jpg
catalog: true
tags:
    - Java
    - 数据结构
---

>Java中static和final的区别


### static关键字

 static 关键字可以用来修饰类的变量，方法和内部类。static 是静态的意思，也是全局的意思它定义的东西，属于全局与类相关，不与具体实例相关。就是说它调用的时候，只是 ClassName.method(),而不是 new ClassName().method()。new ClassName()不就是一个对象了吗？static 的变量和方法不可以这样调用的。它不与具体的实例有关。

### final关键字

final 关键字有三个东西可以修饰的。修饰类，方法，变量。  详细解释一下：

**（1）在类的声明中使用 final**

使用了 final 的类不能再派生子类，就是说不可以被继承了。有些 java 的面试题里面，问 String 可不可以被继承。答案是不可以，因为 java.lang.String是一个 final 类。这可以保证 String 对象方法的调用确实运行的是 String 类的方法，而不是经其子类重写后的 方法。

**（2）在方法声明中使用 final**

被定义为 final 的方法不能被重写了，如果定义类为 final 的话，是所有的方法都不能重写。而我们只需要类中的某几个方法，不可以被重写，就在方法前加 final 了。而且定义为 final 的方法执行效率要高的啊。

**（3）在变量声明中使用 final**

这样的变量就是常量了，在程序中这样的变量不可以被修改的。修改的话编译器会抱错的。而且执行效率也是比普通的变量要高。final 的变量如果没有赋予初值的话，其他方法就必需给他赋值，但只能赋值一次。

注意：子类不能重写父类的静态方法哦，也不能把父类不是静态的重写成静态的方法。想隐藏父类的静态方法的话，在子类中声明和父类相同的方法就行了。

static final为什么要一起用：

final与static final的区别是：final在一个对象类唯一，static final在多个对象中都唯一；

一个既是static又是final的域只占据一段不能改变的存储空间，只有一份。

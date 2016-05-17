---
title: Python Generator
date: 2016-05-12 14:50:41
categories: Python
tags: Python
---

说完了装饰器，今儿干脆也学习一下生成器吧..

> **生成器**是这样一个函数，它记住上一次返回时在函数体中的位置。对生成器函数的第二次（或第 n 次）调用跳转至该函数中间，而上次调用的所有局部变量都保持不变。
>
> 生成器不仅“记住”了它数据状态；生成器还“记住”了它在流控制构造（在命令式编程中，这种构造不只是数据值）中的位置。
>
> **生成器的特点**：
>
> 生成器是一个函数，而且函数的参数都会保留。
>
> 迭代到下一次的调用时，所使用的参数都是第一次所保留下的，即是说，在整个所有函数调用的参数都是第一次所调用时保留的，而不是新创建的
>
> yield就是这样的一个生成器。
>
> **yield 生成器的运行机制：**
>
> 当你问生成器要一个数时，生成器会执行，直至出现 yield 语句，生成器把 
>
> yield 的参数给你，之后生成器就不会往下继续运行。 当你问他要下一个数时，他会从上次的状态。开始运行，直至出现yield语句，把参数给你，之后停下。如此反复直至退出函数。
>
> **yield的使用**：
>
> 在python中，当你定义一个函数，使用了yield关键字时，这个函数就是一个生成器，它的执行会和其他普通的函数有很多不同，函数返回的是一个对象，而不是你平常所用return语句那样，能得到结果值。如果想取得值，那得调用next()函数，如：
>
> 

<!--more-->

举个例子

每次函数运算到yield位置会冻结运行状态，知道被调用next()方法会返回yield的数据，并且继续运行到下一次yield的位置，再次冻结。如下是一个简单的菲波那切数列生成器：

```
def fib(max):  
    a, b = 1, 1  
    while a < max:  
        yield a #generators return an iterator that returns a stream of values.  
        a, b = b, a+b 
        
for n in fib(15):  
    print n  
```

    1
    1
    2
    3
    5
    8
    13

直接初始化这个生成器的话，由于没有进行实例化，内存中并不会保存运行中的状态。

```
def fib(max):  
    a, b = 1, 1  
    while a < max:  
        yield a #generators return an iterator that returns a stream of values.  
        a, b = b, a+b 
```


```
fib(1)
```




    <generator object fib at 0x035D6698>


在for.. in..循环中，每次遍历到生成器都相当于运行了next()方法

```
a = fib(15)
```

```
a.next()
```
    1
```
a.next()
```
    1
```
a.next()
```
    2
```
a.next()
```
    3
```


```

如果使用迭代器实现的话，是如下形式的

```
class Fib:  
    def __init__(self, max):  
        self.max = max  
    def __iter__(self):  
        self.a = 0  
        self.b = 1  
        return self  
    def next(self):  
        fib = self.a  
        if fib > self.max:  
            raise StopIteration  
        self.a, self.b = self.b, self.a + self.b  
        return fib 
for n in fib(15):  
    print n 


    1
    1
    2
    3
    5
    8
    13
```
基本上只是照搬了例子，如果不找机会做一下实践肯定是记不住的。
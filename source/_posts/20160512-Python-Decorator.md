---
title: 20160512 Python Decorator
date: 2016-05-11 18:51:36
categories: Python
tags: [Python]
---


最近看了几个文章，打算把python的装饰器生成器迭代器几个概念好好梳理一下


[Python Decorators Overview](http://pythoncentral.io/python-decorators-overview/)

[Python装饰器与面向切面编程](http://www.cnblogs.com/huxi/archive/2011/03/01/1967600.html)

[Python——functools](http://www.cnblogs.com/Security-Darren/p/4168310.html)


# 装饰器
装饰器原来我一直感觉是一个高大上的玩意儿，一直也没有好好研究过。
不过研究了一下其实还是蛮简单的。
首先下面是一个最简单的例子：


```python
def decorator(fn):
    def inner(n):
        return fn(n) + 1
    return inner
 
@decorator
def f(n):
    return n + 1

print f(1)
```


    3


在这里面decorator就是一个装饰器，调用f()的时候相当于执行了decorator（f()）
f(n)的输出为n+1，在经过装饰器之后在decorator的inner()中又+1了一次，所以输出是1+1+1=3

<!--more-->




```python
@decorator
def foo();
    pass
```

这个形式是python的一个语法糖，仅为了让码农们写起来更爽
当然，看起来也爽一点儿对不,本来面目应该是：


```python
def decorator(fn):
    def inner(n):
        return fn(n) + 1
    return inner
 
def f(n):
    return n + 1
 
f = decorator(f)
print f(1)
```

    3
​    再用一个别人的例子：


```python
def fizz_buzz_or_number(i):
    ''' Return "fizz" if i is divisible by 3, "buzz" if by
        5, and "fizzbuzz" if both; otherwise, return i. '''
    if i % 15 == 0:
        return 'fizzbuzz'
    elif i % 3 == 0:
        return 'fizz'
    elif i % 5 == 0:
        return 'buzz'
    else:
        return i
 
for i in range(1, 31):
    print(fizz_buzz_or_number(i))
```

这一段脚本的执行结果应该是在控制台打印31行内容，如果想最小改动将内容保存在文本文件中，就可以用装饰器来实现


```python
def log_calls(fn):
    ''' Wraps fn in a function named "inner" that writes
        the arguments and return value to logfile.log '''
    def inner(*args, **kwargs):
        # Call the function with the received arguments and
        # keyword arguments, storing the return value
        out = apply(fn, args, kwargs)
 
        # Write a line with the function name, its
        # arguments, and its return value to the log file
        with open('logfile.log', 'a') as logfile:
            logfile.write(
                '%s called with args %s and kwargs %s, returning %s\n' %
                (fn.__name__,  args, kwargs, out))
 
        # Return the return value
        return out
    return inner
```


```python
@log_calls
def fizz_buzz_or_number(i):
    # Do something
    
    
for i in range(1, 31):
    print(fizz_buzz_or_number(i))
```

这样执行结果应该是如下log
fizz_buzz_or_number called with args (1,) and kwargs {}, returning 1
fizz_buzz_or_number called with args (2,) and kwargs {}, returning 2
fizz_buzz_or_number called with args (3,) and kwargs {}, returning fizz
fizz_buzz_or_number called with args (4,) and kwargs {}, returning 4
fizz_buzz_or_number called with args (5,) and kwargs {}, returning buzz
fizz_buzz_or_number called with args (28,) and kwargs {}, returning 28
fizz_buzz_or_number called with args (29,) and kwargs {}, returning 29
fizz_buzz_or_number called with args (30,) and kwargs {}, returning fizzbuzz

> apply()函数为python內建函数，是将目标方法当做第一个参数，目标方法的参数当做输入参数来获得输出


## 装饰器叠加

装饰器还可以叠加使用


```python
def b(fn):
    return lambda s: '<b>%s</b>' % fn(s)
 
def em(fn):
    return lambda s: '<em>%s</em>' % fn(s)
 
@b
@em
def greet(name):
    return 'Hello, %s!' % name

greet("Hi")
```




    '<b><em>Hello, Hi!</em></b>'



## 装饰器输入参数

对于不同的装饰函数，也可以使用输入参数来改变装饰器，如下：


```python
def tag_wrap(tag):
    def decorator(fn):
        def inner(s):
            return '<%s>%s' % (fn(s), tag)
        return inner
    return decorator
 
@tag_wrap('b')
@tag_wrap('em')
def greet(name):
    return 'Hello, %s!' % name
 
print(greet('world'))
```

    <<Hello, world!>em>b
​    

## Python内置装饰器

内置的装饰器有三个，分别是staticmethod、classmethod和property，作用分别是把类中定义的实例方法变成静态方法、类方法和类属性。

### @property

属性装饰器可以将方法变成属性，使用setter来写入属性，否则为只读


```python
def decorator(fn):
    def inner(n):
        return fn(n) + 1
    return inner
 
@decorator
def f(n):
    return n + 1

f(1)
```




    3




```python
class Student(object):
    @property
    def score(self):
        try:
            return self._score
        except:
            return "Unset"
    @score.setter
    def score(self,value):
        if not isinstance(value,int):
            raise TypeError('score must be integer')
        if value<0 or value>100:
            raise ValueError('score must between 0~100')
        self._score=value

s=Student()
print s.score
s.score=10
print s.score
s.score=110
print s.score
```

    Unset
    10



    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-33-3a34d85260b9> in <module>()
         18 s.score=10
         19 print s.score
    ---> 20 s.score=110
         21 print s.score


    <ipython-input-33-3a34d85260b9> in score(self, value)
         11             raise TypeError('score must be integer')
         12         if value<0 or value>100:
    ---> 13             raise ValueError('score must between 0~100')
         14         self._score=value
         15 


    ValueError: score must between 0~100



```python
class Student(object):
    @property
    def score(self):
        try:
            return self._score
        except:
            return "Unset"
    @score.setter
    def score(self,value):
        if not isinstance(value,int):
            raise TypeError('score must be integer')
        if value<0 or value>100:
            raise ValueError('score must between 0~100')
        self._score=value

s=Student()
print s.score
s.score=10
print s.score
s.score=110
print s.score
```

    Unset
    10



    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-33-3a34d85260b9> in <module>()
         18 s.score=10
         19 print s.score
    ---> 20 s.score=110
         21 print s.score


    <ipython-input-33-3a34d85260b9> in score(self, value)
         11             raise TypeError('score must be integer')
         12         if value<0 or value>100:
    ---> 13             raise ValueError('score must between 0~100')
         14         self._score=value
         15 


    ValueError: score must between 0~100


### @staticmethod

### @classmethod

## functools模块
### wraps(wrapped[, assigned][, updated]): 
这个装饰器用来保留被装饰的函数的特殊属性


```python
import time
import functools

def timeit(func):
    #@functools.wraps(func)
    def wrapper():
        start = time.clock()
        func()
        end =time.clock()
        print 'used:', end - start
    return wrapper

@timeit
def foo():
    print 'in foo()'

foo()
print foo.__name__
```

    in foo()
    used: 0.000116023983049
    wrapper



```python
import time
import functools

def timeit(func):
    @functools.wraps(func)
    def wrapper():
        start = time.clock()
        func()
        end =time.clock()
        print 'used:', end - start
    return wrapper

@timeit
def foo():
    print 'in foo()'

foo()
print foo.__name__
```

    in foo()
    used: 0.000210628153845
    foo


### total_ordering(cls): 
这个装饰器用于给类添加比较的功能，需要至少实现__lt__、__le__、__gt__、__ge__其中一个

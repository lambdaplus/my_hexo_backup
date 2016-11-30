---
title: Python函数式编程
date: 2016-10-11 18:40:52
tags: Python
---
讲起函数式编程，我首先想到的就是`scheme`。但是今天研究一下`python`的函数式编程。所谓[函数式编程](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B8%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80)即
>函数式编程（英语：functionalprogramming）或称函数程序设计，又称泛函编程，是一种编程范型，它将电脑运算视为数学上的函数计算，并且避免使用程序状态以及易变对象。函数编程语言最重要的基础是λ演算（lambda calculus）。而且λ演算的函数可以接受函数当作输入(引数）和输出（传出值）。
~~维基百科上抄了一把~~

python内置了`map` `filter` `reduce`三个函数。可以用来进行函数式编程。

## 1. Map
```python
map(func, *iterables) --> map object

Make an iterator that computes the function using arguments fromeach of the iterables.  Stops when the shortest iterable is exhausted.
```
从`help(map)`中可以看到。map接受两个参数：函数(func)和可迭代对象(iterables)。最终返回一个`map`对象！
```
In [26]: def square(x):
    ...:     return x ** 2

In [27]: m = map(square, list(range(10)))

In [28]: m
Out[28]: <map at 0x7f53de4d0588>

In [29]: list(m)
Out[29]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
In [131]: list(m)
Out[131]: [] # 再次list发现，输出为空。说明map对象是一次性的，其内部可能是用生成器实现的。待会自己实现一个看看。

```
当然可以使用`for`循环来做上述事情。
```python
In [30]: seq = []

In [31]: for i in range(10):
    ...:     s = square(i)
    ...:     seq.append(s)
    ...:     

In [32]: seq
Out[32]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```
可见，`map`更加优雅。
## 2. Filter
```python
filter(function or None, iterable) --> filter object

Return an iterator yielding those items of iterable for which function(item)is true. If function is None, return the items that are true.

```
`filter` 顾名思义，就是过滤。其接受一个或两个参数。
分两种情况。

1. 类似`map`： 两个参数`func`和`iterable`。返回`func(item)`为`True`的`item`
2.  一个参数`iterable`。返回为`True`的`item`
下面，写一个筛选质数的函数。
```python
def prime_num(x):
    root_x = int(sqrt(x)) + 1
    for i in range(2, root_x):
        if x % i == 0:
            return True
        else:
            continue
    return True

f = filter(prime_num, list(range(100)))
# filter对象其实也是一次性的
```

## 3. Reduce
```python
reduce(function, sequence[, initial]) -> value
    
Apply a function of two arguments cumulatively to the items of a sequence,from left to right, so as to reduce the sequence to a single value.For example, reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]) calculates((((1+2)+3)+4)+5).  If initial is present, it is placed before the items of the sequence in the calculation, and serves as a default when the sequence is empty.
```
`reduce`在我的印象中，就是对一个序列进行计算，计算的方式取决于其第一个参数`function`。目前，我也仅仅用`reduce`做一些数值运算而已。哈哈
```python
In [132]: from functools import reduce
In [137]: def add(x, y):
     ...:     return x + y
     ...: 

In [138]: reduce(add, list(range(100)))
Out[138]: 4950

In [139]: reduce(add, list(range(100)), 100)
Out[139]: 5050
# reduce 第三个(可选)参数的作用
In [140]: reduce(add, list(range(100)), 101)
Out[140]: 5051
```

## 4. Lambda

在`reduce`的的doc中，有一个`lambda`，其可用于创建匿名函数。我们可以把`square`和`add`函数全部用`lambda`创建。
```python
In [145]: m = map(lambda x: x ** 2, list(range(10)))

In [146]: list(m)
Out[146]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
---
In [147]: reduce(lambda x, y: x + y, list(range(100)))
Out[147]: 4950

```

## 实现`map`， `filter`， `reduce`
### 1. map
刚在说了要实现`map`，挖的坑要填。1 2 3 开始。
```
In [149]: L = []
In [150]: def my_map(func, iterable):
     ...:     for i in iterable:
     ...:         i = func(i)
     ...:         L.append(i)
     ...:     return L
     ...: 

In [151]: m = my_map(lambda x: x**2, list(range(10)))

In [152]: m
Out[152]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```
但是此时的`my_map`并不是一次性的。而是一个`list`，没有达到要求。再完善下，要不，午觉都睡不好。
```python
In [155]: def my_map(func, *seq):
    ...:     try:
    ...:         isinstance(seq, Iterable) is True
    ...:         for i in seq:
    ...:             i = func(i)
    ...:             yield i
    ...:     except TypeError as e:
    ...:         print('TypeError: {}'.format(e))
    ...:         

     
In [156]: m = my_map(lambda x: x**2, list(range(10)))

In [157]: m
Out[157]: <generator object my_map at 0x7f53de4d2d58>

In [158]: list(m)
Out[158]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]


In [159]: list(m)
Out[159]: []
```
使用`yield`把其变成生成器貌似有点像啦。简略的实现下。先这样，困死啦！明天回家(2016 10 02)。给祖国母亲过生日,哈哈。

昨天(2016 10 09)回来了，今天继续干。

### 2. filter
```python
In [9]: def my_filter(func, seq):
   ...:     for i in seq:
   ...:         if func(i):
   ...:             yield i

```
实现的很拙劣！

### 3. reduce
再来看看`reduce`。
```python

```

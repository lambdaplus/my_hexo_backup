
---
title: 二分法查找
date: 2016-10-09 11:30:26
tags: Algorithm
---
几个惊艳的递归！

1. 第一次见到这种方法求和，真是吓了我一跳！
```python
In [10]: def binary_sum(S, lft=None, rgt=None):
    ...:     if lft is None:
    ...:         lft = 0
    ...:     if rgt is None:
    ...:         rgt = len(S)
    ...:         
    ...:     if lft > rgt:
    ...:         return 0
    ...:     elif lft == rgt - 1:
    ...:         return S[lft]
    ...:     else:
    ...:         mid = (lft + rgt) // 2
    ...:         return binary_sum(S, lft, mid) + binary_sum(S, mid, rgt)

```
2. 高效率的fibonacci seq

```python
In [17]: def good_fib(n):
    ...:     if n <= 1:
    ...:         return (n, 0)
    ...:     else:
    ...:         (a, b) = good_fib(n-1)
    ...:         return (a + b, a)

```
与普通的相比
```python
In [31]: def fib(n):
    ...:     if n <= 1:
    ...:         return n
    ...:     else:
    ...:         return fib(n-2) + fib(n-1)

```
good_fib()函数的复杂度为O(n),而fib()函数的复杂度为指数级！


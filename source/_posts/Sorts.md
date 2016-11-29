---
title: Python的几种排序
date: 2016-10-04 16:40:59
tags: Algorithm 
---
### 快排
```python
import random

def quick_sort(L):
    if len(L) < 2:
        return L
    
    mid = random.choice(L)
    left = [x for x in L if x < mid]
    right = [x for x in L if x >= mid]
    
    return quick_sort(left) + quick_sort(right)

if __name__ == '__main__':
    L = [random.randrange(100) for _ in range(10)]
    print(insert_sort(L))
```
### 插入排序
```python
import random

def insert_sort(L):
    if len(L) < 2:
        return L
    
    for i in range(1, len(L)):
        tmp = L[i]
        j = i - 1
        while j >= 0 and L[j] > tmp:
            L[j+1] = L[j]
            j = j - 1
        L[j+1] = tmp # 把tmp赋值给索引最小且值大于tmp的元素
        
    return L

if __name__ == '__main__':
    '''
    ```python
    每次循环的结果，简单表示下
    假设 L = [5, 7, 4, 6, 3]
    i = 1 -> [5, 7, 4, 6, 3]
    i = 2 -> [5, 7, 7, 6, 3] -> [5, 5, 7, 6, 3] -> [4, 5, 7, 6, 3]
    i = 3 -> [4, 5, 7, 6, 3] -> [4, 5, 7, 6, 3] -> [4, 5, 6, 7, 3]
    i = 4 -> [3, 4, 5, 6, 7]
    ```
    '''
    L = [random.randrange(100) for _ in range(10)]
    print(insert_sort(L))
```
### 冒泡排序
```python
import random
def bubble(L):
    if len(L) < 2:
        return L
    
    for n in range(len(L)):
        for i in range(len(L) -1):
            if L[i] > L[i+1]:
                L[i], L[i+1] = L[i+1], L[i]
    return L
if __name__ == '__main__':
    '''
    复杂度为n ** 2
    '''
    L = [random.randrange(100) for _ in range(10)]
    print(bubble(L))
```
### 归并排序
```python
from random import randrange
def merge_sort(seq):
    mid = len(seq) // 2
    lft, rgt = seq[:mid], seq[mid:]
    
    if len(lft) > 1:
        lft = merge_sort(lft)
    if len(rgt) > 1:
        rgt = merge_sort(rgt)
    
    res = []
    while lft and rgt:
        if lft[-1] >= rgt[-1]: #取lft和rgt序列中最大的值
            res.append(lft.pop())
        else:
            res.append(rgt.pop())
    res.reverse()              # 反序一下
    return (lft or rgt) + res
if __name__ == '__main__':
    seq = [randrange(100) for _ in range(10)]
    print(merge_sort(seq))
```
### 选择排序
```python
def sel_sort(seq):
    for i in range(len(seq)-1, 0, -1):
        max_j = i # 预设最大索引 max_j
        for j in range(i): 
            if seq[j] > seq[max_j]:
                max_j = j # 实际最大的 max_j
        seq[i], seq[max_j] = seq[max_j], seq[i] # 交换最大值
    return seq
if __name__ == "__main__":
    seq = [5, 3, 6, 9, 8, 2]
    print(sel_sort(seq))
```

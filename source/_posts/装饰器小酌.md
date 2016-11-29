
---
title: 装饰器小酌
date: 2016-11-10 21:30:29
tags: Python 
---
Python中的装饰器真是个好东西。熟悉我的人都知道，挺能喷的一个人。但是，技术不是喷出来的，前言写的再花，也不会让我的装饰器比别人的更有威力。哈哈哈

## 开撸

### 1. 不带参数的
```python
In [17]: from functools import wraps
    ...: 
    ...: def log(func):
    ...:     @wraps(func)
    ...:     def wrapper(*args, **kw):
    ...:         print("I'm a log ^*^")
    ...:         result = func(*args, **kw)
    ...:         return result
    ...:     return wrapper
    ...: 

In [19]: @log
    ...: def hello():
    ...:     print('Hello World')
    ...:     

In [20]: hello()
I'm a log ^*^
Hello World
```
### 2.带参数的怎么写呢？？？
请看
```python
In [5]: from functools import wraps

In [8]: def logs(file="info.log"):
   ...:     def decorate(func):
   ...:         @wraps(func)
   ...:         def wrapper(*args, **kw):
   ...:             log = func.__name__ + " was called"
   ...:             print(log)
   ...:             with open(file, 'a') as f:
   ...:                 f.write(log + '\n')
   ...:         return wrapper
   ...:     return decorate
   ...: 

In [11]: @logs()
    ...: def hello():
    ...:     print('Hello World!')
    ...:     

In [12]: hello()
hello was called

In [13]: @logs(file='info2.log')
    ...: def hello2():
    ...:     print('Hello World!')
    ...:     

In [14]: hello2()
hello2 was called
```

### 3. flask_login.login_required
flask_login.login_required 是也是一个装饰器，login_required 装饰器的主要作用就是让只有已登陆和认证过的用户才能继续调用被其装饰的视图(view)函数。下面是截取的 **片段**
```python

def login_required(func):
    @wraps(func)
    def decorated_view(*args, **kwargs):
        if request.method in EXEMPT_METHODS:
            return func(*args, **kwargs)
        elif current_app.login_manager._login_disabled:
            return func(*args, **kwargs)
        elif not current_user.is_authenticated:
            return current_app.login_manager.unauthorized()
        return func(*args, **kwargs)
    return decorated_view

```
就到这里吧，反正很少有人会来看。就一个人浪吧。

---
~~更新~~
---
### 4.类装饰器
```python
In [1]: class Log():
   ...:     def __init__(self, file="info.log"):
   ...:         self.file = file
   ...:     def __call__(self, func):
   ...:         log = func.__name__  + " was called"
   ...:         print(log)
   ...:         with open(self.file, 'a') as f:
   ...:             f.write(log+'\n')
   ...: 

In [2]: @Log()
   ...: def hello():
   ...:     print('Hello World!')
   ...:     
hello was called

```
可复制粘贴代码在[这里](https://github.com/lambdaplus/python/tree/master/decorate)
## 参考
1. 哦，对了，附上一个[牛逼的链接](http://stackoverflow.com/questions/739654/how-to-make-a-chain-of-function-decorators-in-python/1594484#1594484)，墙裂推荐不熟悉装饰器的小伙伴们看看。

2. [Python tips Decorates](http://book.pythontips.com/en/latest/decorators.html#decorator-classes)

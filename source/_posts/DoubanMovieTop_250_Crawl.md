﻿
---
title: 豆瓣电影Top250爬虫
date: 2016-10-24 21:30:29
tags: 爬虫
---

## 爬取豆瓣电影top250。

## 1. 单线程版
```python
# -*- coding: utf-8 -*-

import requests
import re
from threading import Thread
from bs4 import BeautifulSoup as bs

def fetch(url):
    s = requests.Session()
    s.headers.update({"user-agent": user_agent})
    return s.get(url)
    
def title_get(url):
    try:
        result = fetch(url)
    except requests.exceptions.RequestException:
        return False
    html = bs(result.text, 'lxml')
    title_list = html.select('div.pic > a > img')
     '''
    title_list中的元素格式如下 e.g: 
     <img alt="这个杀手不太冷" class="" src="https://img3.doubanio.com
     /view/movie_poster_cover/ipst/public/p511118051.jpg"/
    '''
    try:
        title = [re.findall(r'alt="(.*?)"', str(title))[0] for title in title_list]
    except IndexError:
        pass
    return title
    
def not_use_thread():
    for page in range(0, 250, 25):
        url = 'https://movie.douban.com/top250?start={}&filter='.format(page)
        title_get(url)
        
if __name__ == '__main__':
    user_agent = 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 \
                (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36'
    %time not_use_thread() # 我使用的Ipython %time是其自带的模块 下面是其输出！
    
Out： CPU times: user 1.11 s, sys: 8 ms, total: 1.12 s
Wall time: 3.58 s
```

## 2. 多线程版
```python
# -*- coding: utf-8 -*-

import requests
import re
from threading import Thread
from bs4 import BeautifulSoup as bs

def fetch(url):
    s = requests.Session()
    s.headers.update({"user-agent": user_agent})
    return s.get(url)
    
def title_get(url):
    try:
        result = fetch(url)
    except requests.exceptions.RequestException:
        return False
    html = bs(result.text, 'lxml')
    title_list = html.select('div.pic > a > img')
    try:
        title = [re.findall(r'alt="(.*?)"', str(title))[0] for title in title_list] 
    except IndexError:
        pass
    return title
    
def use_thread():
    threads = []
    for page in range(0, 250, 25):
        url = 'https://movie.douban.com/top250?start={}&filter='.format(page)
        t = Thread(target=title_get, args=(url, ))
        t.setDaemon(True)
        threads.append(t)
        t.start()
        
    for t in threads:
        t.join()
        
if __name__ == '__main__':
    user_agent = 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 \
                (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36'
    %time use_thread()
    
Out： CPU times: user 1.16 s, sys: 172 ms, total: 1.33 s
Wall time: 1.28 s
```
### 使用线程池
线程的创建和销毁是一个比较重的开销。所以，使用线程池，重用线程池中的线程！

```python
def use_thread_pool():
    url = 'https://movie.douban.com/top250?start={}&filter='
    urls = [url.format(page) for page in range(0, 250, 25)]
    pool = ThreadPool(7)
    pool.map(title_get, urls)
    pool.close()
    pool.join()
        
Out： CPU times: user 1.23 s, sys: 152 ms, total: 1.38 s
Wall time: 1.29 s
```
再加上一个异步的吧
## 3. 异步版
此版本使用的是异步库`asyncio`和对其进行深度封装的库`aiohttp`。
```python
# coding=utf-8

import re
import aiohttp
import asyncio
from bs4 import BeautifulSoup

async def get(url, headers):
    res = await aiohttp.request('GET', url)
    body = res.read()
    return (await body)

def get_title(html, name=None):
    soup = BeautifulSoup(html, 'lxml')
    title_list = soup.select('div.pic > a > img')
    try:
        title = [re.findall(r'alt="(.*?)"', str(title))[0] for title in title_list]
    except IndexError:
        pass
    return title
        

async def print_title(page):
    url = 'https://movie.douban.com/top250?start={}&filter='.format(page)
    with await sem:
        html = await get(url, headers)
    title = get_title(html)
#    print('{} {}'.format(page, title))
    
if __name__ == '__main__':
    headers = {'User-Agent':'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 \
                (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36'}
    pages = list(range(0, 250, 25))
    sem = asyncio.Semaphore(5) # 限制并发量
    loop = asyncio.get_event_loop()
    f = asyncio.wait([print_title(page) for page in pages])
    %time loop.run_until_complete(f)
    
Out: CPU times: user 984 ms, sys: 28 ms, total: 1.01 s
Wall time: 1.67 s
```
## 总结

**以上测试时间基于笔者电脑的配置和网络情况, 因人而异！**

1. 单线程和多线程的对比，可以看到，使用多线程后速度提升了3倍。
2. 使用线程池后，在限制线程数的状态下，依然有着不错的速度！
3. 使用异步虽然在这里并没有多大的优势相对于多线程来说，但是当请求量很大时，就能显示出异步的强大了。在这里就不做过多赘述了！

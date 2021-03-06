﻿---
title: 一个异步爬虫
date: 2016-11-19 22:30:19
tags: 爬虫
---

看了好多天的异步，今天终于算是大致理解了。模仿着写了一个异步小爬虫。以前很不理解哪里要使用异步，搞的头大。对于爬虫来说，耗时的地方是对服务器的请求，于是把对网页的请求使用异步即可！
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
    '''
    title_list中的如下格式
     <img alt="这个杀手不太冷" class="" src="https://img3.doubanio.com
     /view/movie_poster_cover/ipst/public/p511118051.jpg"/
    '''
    try:
        title = [re.findall(r'alt="(.*?)"', str(title))[0] for title in title_list]
    except:
        pass
    return title
        

async def print_title(page):
    url = 'https://movie.douban.com/top250?start={}&filter='.format(page)
    with await sem:
        html = await get(url, headers)
    title = get_title(html)
    print('{} {}'.format(page, title))
    
if __name__ == '__main__':
    headers = {'User-Agent':'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 \
                (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36'}
    pages = list(range(0, 250, 25))
    sem = asyncio.Semaphore(4) # 限制协程并发量
    loop = asyncio.get_event_loop()
    f = asyncio.wait([print_title(page) for page in pages])
    %time loop.run_until_complete(f) # %time 为Ipython 自带功能模块
    
Out：CPU times: user 884 ms, sys: 12 ms, total: 896 ms
Wall time: 1.27 s
```
随着 `sem=asyncio.Semaphore(4)`中Semaphore限制的减少，此程序越来越快！

比我去年的写的一只小爬虫不知道快到那里去
```python
# -*- coding: utf-8 -*-

import requests
import re
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

def not_use_thread():
    for page in range(0, 250, 25):
        url = 'https://movie.douban.com/top250?start={}&filter='.format(page)
        title_get(url)
        
if __name__ == '__main__':
    user_agent = 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 \
                (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36'
    %time not_use_thread()
    
Out: CPU times: user 1.07 s, sys: 20 ms, total: 1.09 s
Wall time: 7.26 s
```




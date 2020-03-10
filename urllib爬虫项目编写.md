---
title: urllib爬虫项目编写
date: 2020-02-29 20:00:13
tags:
---

**urllib模块是一个可以用于编写爬虫的非常常用的模块。**

### 爬取网页

打开python自带的编辑器idle ，导入urllib模块和urllib.request

```
import urllib
import urllib.request
```

**1.若想将某个站点，网站爬去到内存中，可以**

```
data=urllib.request.urlopen("http://www.jd.com").read().decode("utf-8","ignore")
```

是否爬取到数据？判断数据大小  len(data) 。

怎么从数据data中提取出标题？

```
import re
pat="<title>(.*?)</title>"
re.compile(pat,re.S).findall(data)
```

**2.爬到硬盘的文件中**

```
url.request.urlretrieve("http://www.jd.com",filename="C:\\Users\\demoncigar\\Desktop\\jd.html")
```

### 浏览器伪装

很多时候，网站会拒绝爬虫的连接，此时需要把爬虫伪装成浏览器。

核心就是改变User-Agent的值，这个值可以在浏览器network中查找复制下来。

```
opener=urllib.request.build_opener()
UA=("User-Agent","Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.70 Safari/537.36")
opener.addheaders=[UA]
urllib.request.install_opener(opener)
data=urllib.request.urlopen("https://www.qiushibaike.com/").read().recode("utf-8","ignore")
```

### (构建)用户代理池

让对方服务器更难识别“我”是爬虫

取多个浏览器的User-Agent的值放到数组里

```
import urllib.request
import random
uapools=[
	"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.70 Safari/537.36",
	"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.70 Safari/537.36",
	"Mozilla/4.0 (compatible;MSIE 7.0;Windows NT 5.1; Maxthon 2.0)"
]
def UA():
	opener=urllib.request.build_opener()
	thisua=random.choice(uapools)
	ua=("User-Agent",thisua)
	opener.addheaders=[ua]
	urllib.request.install_opener(opener)
	print("当前使用UA: "+str(thisua))
url="https://www.qiushibaike.com/"
for i in range(0,10):
	UA()
	data=urllib.request.urlopen(url).read().decode("utf-8","ignore")
	print(len(data))
```

#### 如何实现每爬3次换一次UA

```
for i in range(0,10):
	if i%3==0:
		UA()
	data=urllib.request.urlopen(url).read().decode("utf-8","ignore")
	print(len(data))
```

### 实战：批量爬取糗事百科段子数据

目标站点：https://www.qiushibaike.com/

目标数据：热门段子

要求：实现自动翻页

```
import urllib.request
import re
import random
import time
uapools=[
	"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.70 Safari/537.36",
	"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.70 Safari/537.36",
	"Mozilla/4.0 (compatible;MSIE 7.0;Windows NT 5.1; Maxthon 2.0)"
]
def UA():
	opener=urllib.request.build_opener()
	thisua=random.choice(uapools)
	ua=("User-Agent",thisua)
	opener.addheaders=[ua]
	urllib.request.install_opener(opener)
	print("当前使用UA: "+str(thisua))
for i in range(0,35):
	UA()
	thisurl="http://www/qiushibaike.com/8hr/page/"+str(i+1)+"/?s=4948859"
	data=urllib.request.urlopen(thisurl).read().decode("utf-8","ignore")
	pat='<div class="content">.*?<span>(.*?)</span>.*?</div>'
	rst=re.compile(pat,re.S).findall(data)
	for j in range(0,len(rst)):
		print(rst[j])
		print("----------")
```

若出现报错，可以进行异常处理

```
for i in range(0,35):
	UA()
	thisurl="http://www/qiushibaike.com/8hr/page/"+str(i+1)+"/"
	try:
        data=urllib.request.urlopen(thisurl).read().decode("utf-8","ignore")
        pat='<div class="content">.*?<span>(.*?)</span>.*?</div>'
        rst=re.compile(pat,re.S).findall(data)
        for j in range(0,len(rst)):
            print(rst[j])
            print("----------")
     except Exception as err:
     	pass
```


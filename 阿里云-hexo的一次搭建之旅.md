---
title: 阿里云+hexo的一次搭建之旅
date: 2019-02-15 18:24:33
categories:
- 后端
tags:
- git
- nginx
---



[从零搭建Hexo博客并部署阿里云服务器](https://blog.csdn.net/NoCortY/article/details/99631249"示例链接")

[带你跳过各种坑，一次性把 Hexo 博客部署到自己的服务器](https://blog.csdn.net/qq_35561857/article/details/81590953)



### 服务器

​      搭建博客，之前用gitpage搭建过，但访问速度和空间都有限制，体验一般，就抱着学习的目的买了人生的第一个服务器---阿里云。其实也能选择腾讯云，京东云，百度云....都有优惠，当时京东的最便宜，阿里的服务最全。

​      买了后，就是一系列的系统选择和配置，windows已经很熟悉了，最好选择linux，之后远程连接，阿里云自带的workbench很好用，当然也可以用xshell，安装node，nginx，和git。

### 本地

​	本地就是安装hexo，git，node咯。

最坑的就是最后都搭建完成后，nginx显示403，看了git报错日志，发现是/home/www/website这个文件没有权限（-bash: ./xx.sh: Permission denied），遂到这个文件下

```cpp
chmod 777 /home/www/website
```

赋予权限，就好啦！








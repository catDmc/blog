---
title: node.js
date: 2020-03-01 19:06:31
tags:
---

## node.js 是啥？

1.node.js是一个开发平台，就像java开发平台，.net开发平台，php开发平台，apple开发平台一样。

2.该平台使用的编程语言是js

3.node.js平台是基于Chrome V8 JavaScript 引擎构建

4.基于node.js可以开发控制台程序（命令行程序，cli程序），桌面应用程序（GUI）（借助 node-webkit, electron等框架实现），web应用程序（网站）

node.js全栈开发技术栈：MEAN - MongoDB ,Express , vue/react/angular,node.js

### node.js的特点

1.事件驱动（当事件被触发时，执行传递过去的回调函数）

2.非阻塞 I/O模式（当执行I/O操作时，不会阻塞线程）

3.单线程

4.拥有世界最大的开源库生态系统 — npm

#### 通过nvm-windows 管理一台计算机上的多个node版本

[下载nvm](https://github.com/coreybutler/nvm-windows)

常用命令：

​	nvm version

​	nvm install latest  (安装nodejs最新版本)

​	nvm install 版本号

​	nvm uninstall 版本号

​	nvm list

​	nvm use 版本号 （使用某个版本）

### node.js 开发web应用程序和PHP，Java，ASP.Net等传统模式开发web应用程序区别

传统模式，有**web容器**（例如Apache,IIS,Tomcat等，监听8080端口），nodejs开发web应用程序没有web容器（本身就是，不需要）

### 在node.js上编写程序

#### REPL介绍

**1.REPL 全称：Read-Eval-Print-Loop（交互式解释器）**

R 读取--读取用户输入，解析输入的js数据结构并存储在内存中。

E 执行--执行输入的数据结构

P 打印--输出结果

L 循环--循环操作以上步骤直到用户两次按下 ctrl-c 按钮退出

**2.在REPL中编写程序（类似于浏览器开发人员工具中的控制台功能）**

直接在控制台输入” node“ 命令进入REPL环境

**3.按两次"Control+c"退出REPL界面或者输入".exit"退出REPL界面**

### fs 写入文件操作

模块分全局和非全局

api分三级，0--废弃，1--开发中，2--稳定

```js
// 执行文件操作
// ----文件写入操作
// 1.加载文件操作模块，fs模块
var fs = require('fs');
// 2.实现文件写入操作
var msg = 'hello world'
// 调用 fs.writeFile() 进行文件写入
// fs.writeFile(file,data[,options],callback)
fs.writeFile('./hello.txt',msg.'utf8',function(err){
	// err === null,表示写入文件成功，没有错误！
    if(err){
    	console.log('写入文件出错！')
	}else{
    	console.log('ok')
	}
})
```

### fs 读取文件

1.加载fs 模块

2.调用fs.readFile()方法来读取文件

fs.readFile(file[,option],callback)

```js
var fs = require('fs');
fs.readFile('./hello.txt','utf8',function(err,data){
	if(err){
    	throw err;
}
    //data 参数的数据类型是一个buffer对象，里面是一个个字节（字节数组）
    console.log(data);
  //console.log(data.toString('utf8'));
})
```

### 读取文件的路径问题

__dirname  表示当前正在执行的js文件所在的目录

__filename  表示当前正在执行的js文件的完整路径

它们并不是全局的

```js
var fs = require('fs');
var filename = __dirname+'//'+'hell.txt'
fs.readFile('./hello.txt','utf8',function(err,data){
	if(err){
    	throw err;
}
    //data 参数的数据类型是一个buffer对象，里面是一个个字节（字节数组）
    console.log(data);
  //console.log(data.toString('utf8'));
})
```

**通过path模块进行路径拼接**

### fs模块创建文件夹

```js
var fs = require('fs');
fs.mkdir('./test-mkdir',function(err){
    if(err){
        console.log('创建目录出错')
    }else{
        console.log('目录创建成功')
})
```


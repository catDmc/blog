---
title: js经典设计模式:订阅发布与vue响应式
date: 2019-03-20 21:23:04
tags:
---

**闲来无聊，擦枪走火**

实现一个订阅发布的功能，优化再解耦封装，让我慢慢道来....（不多BB，上代码）

### 1.简单构思实现功能

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    #head{
      font-size:large;
      color:rebeccapurple;
    }
  </style>
</head>
<body>
  <div id="head">
    <p>Soldier keep on marchin' on</p>
  </div>
  <script>
    let houseObj = {}; //发布者
    houseObj.list = [];

    //增加订阅者
    houseObj.listen = function(fn){
      this.list.push(fn);
      console.log(this.list);
    }
    
    //发布消息
    houseObj.trigger = function(){
      for(let i=0,fn;fn = this.list[i];i++){
        fn.apply(this,arguments);//发布消息附带的参数
      }
    }

    //需求订阅
    houseObj.listen(function(size){
      console.log("小红：我要买的房子是"+size+"平米");
    })
    houseObj.listen(function (size) {
        console.log("小明：我要买的房子是" + size + "平米");
      })
    //发布
    houseObj.trigger(100);
    houseObj.trigger(160);
    houseObj.trigger(260);
    
	//多了解了下arguments,求最大值的
    let max = function(){
      let m = arguments[0];
      for(let val of arguments){
        if(val > m){
          m = val
        }
      }
      return m;
    }
    let valMax = max(9,8,4,'1','15');
    console.log(valMax);
  </script>
</body>
</html>

//打印如下
//     [ƒ]
//     (2) [ƒ, ƒ]
//     小红：我要买的房子是100平米
//     小明：我要买的房子是100平米
//     小红：我要买的房子是160平米
//     小明：我要买的房子是160平米
//     小红：我要买的房子是260平米
//     小明：我要买的房子是260平米
//     15
```

### 2.上面代码可以看到打印出了多次，是因为没有指定，没有标示，优化如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    #head{
      font-size:large;
      color:rebeccapurple;
    }
  </style>
</head>
<body>
  <div id="head">
    <p>Soldier keep on marchin' on</p>
  </div>
  <script>
    let houseObj = {}; //发布者
    houseObj.list = [];//缓存列表 数组=>{} 省去了深拷贝的过程  性能优化

    //增加订阅者
    houseObj.listen = function(key,fn){
      // if(!this.list[key]){
      //   this.list[key] = [];
      // }else{
      //   this.list.push(fn)
      // }
      (this.list[key] || (this.list[key] = [])).push(fn)
    }
    
    //发布消息
    houseObj.trigger = function(){
      //取出消息名称 类数组转换数组
      let key = Array.prototype.shift.call(arguments);
      let fns = this.list[key];
      if(!fns || fns.length === 0){
        return;
      }
      for(let i=0,fn;fn = fns[i++];){
        fn.apply(this,arguments);//发布消息附带的参数
      }
    }

    //需求订阅
    houseObj.listen('big',function(size){
      console.log("小红：我要买的房子是"+size+"平米");
    })
    houseObj.listen('small',function (size) {
        console.log("小明：我要买的房子是" + size + "平米");
      })
    //发布
    houseObj.trigger('big',100);
    houseObj.trigger('small',50);
  </script>
</body>
</html>

//打印如下
//小红：我要买的房子是100平米
//小明：我要买的房子是50平米
```

### 3.上面的代码解决了问题，但细想过后，还可以再优化，每次订阅发布都会有一个新的list，占用过多的内存，下面封装成一个对象

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    #head{
      font-size:large;
      color:rebeccapurple;
    }
  </style>
</head>
<body>
  <div id="head">
    <p>Soldier keep on marchin' on</p>
  </div>
  <script>
    let Event = (function(){
      let list = {},
      listen,
      trigger,
      remove;
      listen = function(key,fn){
        (list[key] || (list[key] = [])).push(fn);
      };
      trigger = function(){
        let key = Array.prototype.shift.call(arguments);
        let fns = list[key];
        if(!fns || fns.length === 0){
          return;
        }
        for(let i=0,fn;fn = fns[i++];){
          fn.apply(this,arguments);
        }
      };
      remove = function(key,fn){
        let fns = list[key];
        //如果没有订阅过
        if(!fn){
          fns && (fns.length = 0);
        }else{
          for(let i = fns.length - 1; i >= 0;i--){
            let _fn = fns[i];
            _fn === fn && (fns.splice(i,1));
          }
        }
      }
      return {
        listen: listen,
        trigger: trigger,
        remove: remove
      }
    })();

    Event.listen('big',function(size){
      console.log('小明 ：我要买的房子是'+size+'平米')
    })

    Event.trigger('big',100);
    Event.trigger('big', 200);


  </script>
</body>
</html>
//打印如下
//小明 ：我要买的房子是100平米
//小明 ：我要买的房子是200平米
```

上面的代码就优雅了许多，非常舒服。

### 扩展一下订阅发布，深入vue响应式（双向绑定）

vue.js文件代码如下：

```js
// 订阅器模型
let Dep ={
  clientList:{},
  listen:function(key,fn){
    (this.clientList[key] || (this.clientList[key] = [])).push(fn);
  },
  trigger:function(){
    let key = Array.prototype.shift.call(arguments);
    fns = this.clientList[key];
    if(!fns || fns.length === 0){
      return;
    }
    for(let i = 0,fn;fn = fns[i++];){
      fn.apply(this,arguments);//发布消息附带的消息
    }
  }
}
//劫持的方法
let dataHijack = function({data,tag,datakey,selector}){
  let value = '';
  el = document.querySelector(selector);
  Object.defineProperty(data,datakey,{
    get:function(){
      console.log('我获取到值了');
      return value
    },
    set:function(newValue){
      console.log('我设置了值');
      value = newValue;
      Dep.trigger(tag,newValue);
    }
  });
  //绑定观察者
  Dep.listen(tag,function(text){
    el.innerHTML = text;
  })
}
```

**上面代码的关键是*Object.defineProperty()*，这个方法可以精准控制对象的属性**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    #head{
      font-size:large;
      color:rebeccapurple;
    }
  </style>
</head>
<body>
  <div id="head">
    <p>Soldier keep on marchin' on</p>
    订阅视图-1：<span class="box-1"></span>
  </div>
  <script src="./vue.js"></script>
  <script>
    var dataObj = {};
    dataHijack({
      data:dataObj,
      tag:'view-1',
      datakey:'one',
      selector:'.box-1'
    });
    dataObj.one = '第一个值';
    // dataObj.one = '第一个值2233';
  </script>
</body>
</html>

```

在浏览器控制台，输入dataObj.one = '新值' ，页面视图会立即变化，当然这只是一个简单的双向绑定
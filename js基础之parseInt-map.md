---
title: 'js基础之parseInt,map'
date: 2019-02-20 17:33:30
ategories:
- 前端
tags:
- js
---

## ['1', '2', '3'\].map(parseInt) ？  what & why ?

这是今天在 [Advanced-Frontend组织](https://github.com/Advanced-Frontend/Daily-Interview-Question) 看到一个比较有意思的题目。 主要是讲**JS的映射与解析** 早在 2013年, 加里·伯恩哈德就在微博上发布了以下代码段:

```js
['10','10','10','10','10'].map(parseInt);
// [10, NaN, 2, 3, 4]
```

### parseInt

`parseInt()` 函数解析一个字符串参数，并返回一个指定基数的整数 (数学系统的基础)。

```js
const intValue = parseInt(string[, radix]);
```

`string` 要被解析的值。如果参数不是一个字符串，则将其转换为字符串(使用 ToString 抽象操作)。字符串开头的空白符将会被忽略。

`radix` 一个介于2和36之间的整数(数学系统的基础)，表示上述字符串的基数。默认为10。 `返回值` 返回一个整数或NaN

```js
parseInt(100); // 100
parseInt(100, 10); // 100
parseInt(100, 2); // 4 -> converts 100 in base 2 to base 10
```

**注意：** 在`radix`为 undefined，或者`radix`为 0 或者没有指定的情况下，JavaScript 作如下处理：

- 如果字符串 string 以"0x"或者"0X"开头, 则基数是16 (16进制).
- 如果字符串 string 以"0"开头, 基数是8（八进制）或者10（十进制），那么具体是哪个基数由实现环境决定。ECMAScript 5 规定使用10，但是并不是所有的浏览器都遵循这个规定。因此，永远都要明确给出radix参数的值。
- 如果字符串 string 以其它任何值开头，则基数是10 (十进制)。

更多详见[parseInt | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)

### map

`map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

```js
var new_array = arr.map(function callback(currentValue[,index[, array]]) {
 // Return element for new_array
 }[, thisArg])
```

可以看到`callback`回调函数需要三个参数, 我们通常只使用第一个参数 (其他两个参数是可选的)。 `currentValue` 是callback 数组中正在处理的当前元素。 `index`可选, 是callback 数组中正在处理的当前元素的索引。 `array`可选, 是callback map 方法被调用的数组。 另外还有`thisArg`可选, 执行 callback 函数时使用的this 值。

```js
const arr = [1, 2, 3];
arr.map((num) => num + 1); // [2, 3, 4]
```

更多详见[Array.prototype.map() | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

### 回到真实的事例上

回到我们真实的事例上

```js
['1', '2', '3'].map(parseInt)
```

对于每个迭代`map`, `parseInt()`传递两个参数: **字符串和基数**。 所以实际执行的的代码是：

```js
['1', '2', '3'].map((item, index) => {
	return parseInt(item, index)
})
```

即返回的值分别为：

```js
parseInt('1', 0) // 1
parseInt('2', 1) // NaN
parseInt('3', 2) // NaN, 3 不是二进制
```

所以：

```js
['1', '2', '3'].map(parseInt)
// 1, NaN, NaN
```

由此，加里·伯恩哈德例子也就很好解释了，这里不再赘述

```js
['10','10','10','10','10'].map(parseInt);
// [10, NaN, 2, 3, 4]
```

### 如何在现实世界中做到这一点

如果实际上想要循环访问字符串数组, 该怎么办？ `map()`然后把它换成数字？使用编号!

```js
['10','10','10','10','10'].map(Number);
// [10, 10, 10, 10, 10]
```



## 更多

### 1

- 首先让我们回顾一下，map函数的第一个参数callback：

`var new_array = arr.map(function callback(currentValue[, index[, array]]) { // Return element for new_array }[, thisArg])` 这个callback一共可以接收三个参数，其中第一个参数代表当前被处理的元素，而第二个参数代表该元素的索引。

- 而parseInt则是用来解析字符串的，使字符串成为指定基数的整数。 `parseInt(string, radix)` 接收两个参数，第一个表示被处理的值（字符串），第二个表示为解析时的基数。
- 了解这两个函数后，我们可以模拟一下运行情况

1. parseInt('1', 0) //radix为0时，且string参数不以“0x”和“0”开头时，按照10为基数处理。这个时候返回1
2. parseInt('2', 1) //基数为1（1进制）表示的数中，最大值小于2，所以无法解析，返回NaN
3. parseInt('3', 2) //基数为2（2进制）表示的数中，最大值小于3，所以无法解析，返回NaN

- map函数返回的是一个数组，所以最后结果为[1, NaN, NaN]
- 最后附上MDN上对于这两个函数的链接，具体参数大家可以到里面看 [https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt](https://muyiy.cn/question/js/url) [https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map](https://muyiy.cn/question/js/url)

### 2

在30-seconds-of-code看到一个这个题的变形

```js
let unary = fn => val => fn(val)
let parse = unary(parseInt)
console.log(['1.1', '2', '0.3'].map(parse))
```

> ### unary
>
> Creates a function that accepts up to one argument, ignoring any additional arguments.
>
> Call the provided function, `fn`, with just the first argument given.
>
> ```js
> const unary = fn => val => fn(val);
> ```

其实这个函数名就已经说明该函数可以被用来干啥，unary 单词的意思是一元，元本身是数量（货币）单位，例如一元钱。如果不明白的话，看 30-seconds-of-code 给出的例子以及英文解释也能很快地知道函数的用途。

```
unary` 函数在执行的过程中，会创建并且返回一个函数，该返回函数最多接受一个参数，然后忽略其余的任何参数。 在只给出第一个参数的情况下，调用函数 `unary` 所提供的函数 `fn
```

题外话，在不指定第二个参数的情况下执行 `parseInt`，可能会存在兼容性问题。当然你不传第二个参数 `radix` 或者显式地传 0、`undefined` 也是可以的，因为 `parseInt` 会把这些情况统一处理成第二个参数为 10，然后执行 `parseInt(str, 10)`。也正是因为兼容性问题，[`eslint` 会提示你显式指定 `parseInt` 第二个参数 `radix`](https://eslint.org/docs/rules/radix)
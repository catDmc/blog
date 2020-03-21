---
title: 深入浅出Object.defineProperty
date: 2019-03-21 20:49:07
tags:
---

##### 对象的定义与赋值

经常使用的定义与赋值方法`obj.prop =value`或者`obj['prop']=value`

##### Object.defineProperty()语法说明

`Object.defineProperty()`的作用就是直接在一个对象上定义一个新属性，或者修改一个已经存在的属性



```css
Object.defineProperty(obj, prop, desc)
```

1. obj 需要定义属性的当前对象
2. prop 当前需要定义的属性名
3. desc 属性描述符

一般通过为对象的属性赋值的情况下，对象的属性可以修改也可以删除，但是通过Object.defineProperty()定义属性，通过描述符的设置可以进行更精准的控制对象属性。

##### 属性的特性以及内部属性

javacript 有三种类型的属性

1. 命名数据属性：拥有一个确定的值的属性。这也是最常见的属性
2. 命名访问器属性：通过`getter`和`setter`进行读取和赋值的属性
3. 内部属性：由JavaScript引擎内部使用的属性，不能通过JavaScript代码直接访问到，不过可以通过一些方法间接的读取和设置。比如，每个对象都有一个内部属性`[[Prototype]]`，你不能直接访问这个属性，但可以通过`Object.getPrototypeOf()`方法间接的读取到它的值。虽然内部属性通常用一个双吕括号包围的名称来表示，但实际上这并不是它们的名字，它们是一种抽象操作，是不可见的，根本没有上面两种属性有的那种字符串类型的属性

##### 属性描述符

通过Object.defineProperty()为对象定义属性，有两种形式，且不能混合使用，分别为数据描述符，存取描述符，下面分别描述下两者的区别：

###### 数据描述符 --特有的两个属性（value,writable）



```csharp
let Person = {}
Object.defineProperty(Person, 'name', {
   value: 'jack',
   writable: true // 是否可以改变
})
```

![img](https:////upload-images.jianshu.io/upload_images/5016475-341b21261bd9366c.png?imageMogr2/auto-orient/strip|imageView2/2/w/916/format/webp)



![img](https:////upload-images.jianshu.io/upload_images/5016475-69e4dc45d0a6c764.png?imageMogr2/auto-orient/strip|imageView2/2/w/876/format/webp)

注意，如果描述符中的某些属性被省略，会使用以下默认规则：



![img](https:////upload-images.jianshu.io/upload_images/5016475-9cd41a36735b667d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

###### 存取描述符 --是由一对 getter、setter 函数功能来描述的属性

`get`：一个给属性提供`getter`的方法，如果没有`getter`则为`undefined`。该方法返回值被用作属性值。默认为`undefined`。
 `set`：一个给属性提供`setter`的方法，如果没有`setter`则为`undefined`。该方法将接受唯一参数，并将该参数的新值分配给该属性。默认值为`undefined`。



```jsx
let Person = {}
let temp = null
Object.defineProperty(Person, 'name', {
  get: function () {
    return temp
  },
  set: function (val) {
    temp = val
  }
})
```

![img](https:////upload-images.jianshu.io/upload_images/5016475-96b00b053d6fd42a.png?imageMogr2/auto-orient/strip|imageView2/2/w/854/format/webp)

##### 数据描述符和存取描述均具有以下描述符

1. configrable   描述属性是否配置，以及可否删除
2. enumerable  描述属性是否会出现在for in 或者 Object.keys()的遍历中

###### configrable 代码片段分析

![img](https:////upload-images.jianshu.io/upload_images/5016475-885fbf1df3d6a465.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

configurable:false不能删除属性



![img](https:////upload-images.jianshu.io/upload_images/5016475-65abac28f1baac3b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

configurable:false不能重新定义属性



![img](https:////upload-images.jianshu.io/upload_images/5016475-6cfce802f175016b.png?imageMogr2/auto-orient/strip|imageView2/2/w/942/format/webp)

等价上一张图的代码



![img](https:////upload-images.jianshu.io/upload_images/5016475-a89db7cd533efbcc.png?imageMogr2/auto-orient/strip|imageView2/2/w/882/format/webp)

与上一张图的代码进行对比



![img](https:////upload-images.jianshu.io/upload_images/5016475-fecfb1b9d9d4eee8.png?imageMogr2/auto-orient/strip|imageView2/2/w/1150/format/webp)

configurable:true能删除属性



![img](https:////upload-images.jianshu.io/upload_images/5016475-210b70d4146f46ec.png?imageMogr2/auto-orient/strip|imageView2/2/w/1022/format/webp)

configurable:true能够定义属性



![img](https:////upload-images.jianshu.io/upload_images/5016475-dadc376bbc6c23b1.png?imageMogr2/auto-orient/strip|imageView2/2/w/1086/format/webp)

configurable:false与上图做对照

从以上代码运行结果分析总结可知：

> 1. configurable: false 时，不能删除当前属性，且不能重新配置当前属性的描述符(有一个小小的意外：可以把writable的状态由true改为false,但是无法由false改为true),但是在writable: true的情况下，可以改变value的值
> 2. configurable: true时，可以删除当前属性，可以配置当前属性所有描述符。

###### enumerable 代码片段分析

![img](https:////upload-images.jianshu.io/upload_images/5016475-25ab11c6f042ed86.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)



注意：以下二种区别



![img](https:////upload-images.jianshu.io/upload_images/5016475-d700d59cf2c6251b.png?imageMogr2/auto-orient/strip|imageView2/2/w/898/format/webp)



![img](https:////upload-images.jianshu.io/upload_images/5016475-ed831b5f32e5cac2.png?imageMogr2/auto-orient/strip|imageView2/2/w/882/format/webp)

##### 不变性

1. 对象常量
    结合writable: false 和 configurable: false  就可以创建一个真正的常量属性（不可修改，不可重新定义或者删除）

![img](https:////upload-images.jianshu.io/upload_images/5016475-53ceb8f0c574556c.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

对象常量

1. 禁止扩展
    如果你想禁止一个对象添加新属性并且保留已有属性，就可以使用Object.preventExtensions(...)

![img](https:////upload-images.jianshu.io/upload_images/5016475-e04c673d73514ae1.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

禁止扩展片段1



![img](https:////upload-images.jianshu.io/upload_images/5016475-bd0d1ef4b505d0bb.png?imageMogr2/auto-orient/strip|imageView2/2/w/1058/format/webp)

禁止扩展片段2

在非严格模式下，创建属性gender会静默失败，在严格模式下，将会抛出异常。

1. 密封
    Object.seal()会创建一个密封的对象，这个方法实际上会在一个现有对象上调用object.preventExtensions(...)并把所有现有属性标记为configurable:false。

![img](https:////upload-images.jianshu.io/upload_images/5016475-446c94b28768fbe9.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

密封



所以， 密封之后不仅不能添加新属性，也不能重新配置或者删除任何现有属性（虽然可以改属性的值）

1. 冻结
    Object.freeze()会创建一个冻结对象，这个方法实际上会在一个现有对象上调用Object.seal(),并把所有现有属性标记为writable: false,这样就无法修改它们的值。

   ![img](https:////upload-images.jianshu.io/upload_images/5016475-1f6ad10fa20995ae.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

   冻结

这个方法是你可以应用在对象上级别最高的不可变性，它会禁止对于对象本身及其任意直接属性的修改（但是这个对象引用的其他对象是不受影响的）
 你可以深度冻结一个对象，具体方法为，首先这个对象上调用Object.freeze()然后遍历它引用的所有对象，并在这些对象上调用Object.freeze()。但是一定要小心，因为这么做有可能会无意中冻结其他共享对象。

##### 属性定义和属性赋值

最后一小节，总结一下上述内容

###### 属性定义,通过Object.defineProperty()形式

1. 如果Obj没有名为Prop的自身属性的话：如果Obj是可扩展的话，则创建Prop这个自身属性，否则拒绝
2. 如果Obj已经有了名为Prop的自身属性：则按照下面的步骤重新配置这个属性
3. 如果这个已有的属性是不可配置的，则进行下面的操作会被拒绝



```jsx
1: 将一个数据属性转换成访问器属性，反之变然
2: 改变`[[Configurable]]`或`[[Enumerable]]`
3: 改变[[Writable]]由false变为true
4: 在`[[Writable]]`为`false`时改变`[[Value]]`
5: 改变[[Get]]或[[Set]]
```

1. 否则这个已有的属性可以被重新配置

###### 属性赋值,通过obj.prop = ''prop"形式

1. 如果在原型链上存在一个名为`P`的只读属性（只读的数据属性或者没有`setter`的访问器属性），则拒绝
2. 如果在原型链上存在一个名为`P`的且拥有`setter`的访问器属性，则调用这个`setter`
3. 如果没有名为`P`的自身属性，则如果这个对象是可扩展的，就创建一个新属性，否则，如果这个对象是不可扩展的，则拒绝
4. 如果已经存在一个可写的名为P的自身属性，则调用Object.defineProperty(),该操作只会更改`P`属性的值，其他的特性（比如可枚举性）都不会改变

##### 作用以及影响

属性的定义操作和赋值操作各自有自己的作用和影响。
 **赋值可能会调用原型上的`setter`，定义会创建一个自身属性**。
 **原型链中的同名只读属性可能会阻止赋值操作，但不会阻止定义操作**。如果原型链中存在一个同名的只读属性，则无法通过赋值的方式在原对象上添加这个自身属性，必须使用定义操作才可以。这项限制是在ECMAScript 5.1中引入的

![img](https:////upload-images.jianshu.io/upload_images/5016475-f978709d73c32b5d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)



![img](https:////upload-images.jianshu.io/upload_images/5016475-d6e8aa6461813e68.png?imageMogr2/auto-orient/strip|imageView2/2/w/944/format/webp)



![img](https:////upload-images.jianshu.io/upload_images/5016475-f42ff3eac0792f2a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)



![img](https:////upload-images.jianshu.io/upload_images/5016475-3561b2bb74a80778.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)


 赋值运算符不会改变原型链上的属性
 不能通过为`obj.foo`赋值来改变`proto.foo`的值。这种操作只会在`obj`上新建一个自身属性

![img](https:////upload-images.jianshu.io/upload_images/5016475-ba6affa8f78c4a89.png?imageMogr2/auto-orient/strip|imageView2/2/w/1040/format/webp)


 对象字面量中的属性是通过定义操作添加的。

![img](https:////upload-images.jianshu.io/upload_images/5016475-d91e7e26decfa23b.png?imageMogr2/auto-orient/strip|imageView2/2/w/802/format/webp)



再次啰嗦一次，记住以下两种形式的区别：

![img](https:////upload-images.jianshu.io/upload_images/5016475-16ef54cf96c184da.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

上面的代码等同于：

![img](https:////upload-images.jianshu.io/upload_images/5016475-b4633515fd327cec.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

另一方面：

![img](https:////upload-images.jianshu.io/upload_images/5016475-8a2a3b2da50e0868.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

上面的代码等同于：

![img](https:////upload-images.jianshu.io/upload_images/5016475-f5fa67373fc34d36.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)


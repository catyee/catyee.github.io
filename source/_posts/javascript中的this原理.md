---
title: javascript中的this原理
date: 2018-06-25 16:58:51
tags: 'js'
---
```
    var obj = {
        foo: function() {console.log(this.bar)};
        bar: 1
    }
    var foo = obj.foo;
    var bar = 2;
    obj.foo(); // 1
    foo();// 2
```

上面这段代码中，obj.foo 和foo指向的是同一个函数，但是结果却是不一样的。 

我们都知道，javascript中，this指的是函数运行时所在的环境，obj.foo()运行在obj环境,所以this指向的是obj;foo()运行在全局环境，所以this指向全局环境。但是为什么就说obj.foo()是在obj环境执行，而一旦var foo = obj.foo,foo()就变成了全局环境执行呢？也就是说函数的运行环境到底是由什么决定的。对于这个问题，我们往往是模棱两可的，下面就来解释下javascript这样处理的原理，本文主要参考了阮一峰老师的文章[JavaScript 的 this 原理](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)。

### 内存的数据结构
- var obj = {foo: 5} 我们把一个对象赋值给了一个变量obj。javascript引擎会先在内存里面，生成一个对象{foo: 5},然后把这个对象的内存地址赋值给obj。这也就是说obj实际上是一个地址，后面如果要读取obj.foo,引擎会先从obj拿到内存地址，然后再从该地址读出原始的对象，返回它的foo属性。
 
    原始的对象以字典结构保存，每一个属性名都对象一个属性描述对象，例如上述foo属性实际上是以下面形式保存的。
    ![image](https://i.loli.net/2018/06/25/5b30621703c03.png)

    注意，foo属性的值保存在属性描述对象的value属性里面

### 函数
- 如果属性的值是一个函数，例如 var obj = {foo:function(){}},这时，引擎会将函数单独保存在内存中，然后再将函数的地址赋值给foo属性的value属性
    ![image](https://i.loli.net/2018/06/25/5b30621703e5a.png)

由于函数是一个单独的值，所以它可以在不同的环境（上下文）执行
```
    var f = function() {}
    var obj = {f: f}
    // 单独执行
    f();
    // obj 环境执行
    obj.f();
```
### 环境变量
javascript允许在函数体内部，引用当前环境的其他变量
```
    var f = function() {
        console.log(x);
    }
```
上面代码中，函数体里面使用了变量x，该变量是由运行环境提供的。由于函数可以在不同的运行环境中执行，所以需要一种机制，能够在函数体内部获得当前的运行环境，所以，this出现了，它的设计目的就是在函数体内部，指代函数当前的运行环境。

```
    var f = function() {
        console.log(this.x)
    }
```
上面代码中，函数体里面的this.x 就是当前运行环境的x
```
    var f = function() {
        console.log(this.x);
    }
    var x = 1;
    var obj = {
        f: f,
        x: 2
    }
    // 单独执行
    f(); // 1
    // obj环境执行
    obj.f() // 2
```
上面代码中，函数f在全局执行，this.x指向全局环境x
![image](https://i.loli.net/2018/06/25/5b30894fe4d71.png)
在obj环境执行，this.x指向obj.x
![image](https://i.loli.net/2018/06/25/5b3089e7eea40.png)

回到文章开头的问题，obj.foo()是通过obj找到foo,所以就是在obj环境执行，一旦var foo = obj.foo,变量就直接指向函数本身，所以foo()就变成在全局环境执行。

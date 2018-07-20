---
title: Generator
date: 2018-07-13 15:51:08
tags: ES6
categories: 前端学习笔记(ES6)
---
##### 基本概念
Generator函数是ES6提供的一种异步编程解决方案。

Generator函数有多种理解角度。语法上，首先可以把它理解成是一个状态机，封装了多个内部状态。
执行Generator函数会返回一个遍历器对象，也就是说，Generator函数除了状态机，还是一个遍历器对象生成函数，返回的遍历器对象，可以依次遍历Generator函数内部的每一个状态。
形式上，Generator函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之前有一个星号;二是，函数体内部使用yield(产出)表达式，定义不同的内部状态。
```
    function* helloWorldGenerator() {
        yield 'hello';
        yield 'world';
        return 'ending';
    }
    var hw = helloWorldGenerator();
```
上面代码定义了一个Generator函数helloWorldGenerator，它内部有两个yield表达式(hello和world)，即该函数有三个状态: hello,world和return语句(结束执行)。
然而，Generator函数的调用方法和普通函数一样，不同的是,调用Generator函数后，该函数并不执行，返回的也不是函数的执行结果，而是一个指向内部状态的指针对象。即遍历器对象。
下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态，也就是说每次调用next方法，内部指针就从函数头部或者上一次停下来的地方开始执行，直到遇到下一个yield表达式(或return语句)为止。也就是说，Generator函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行
```
    hw.next(); // {value: 'hello', done: false}
    hw.next(); // {value: 'world', done: false}
    hw.next(); // {value: 'ending', done: true}
    hw.next(); // {value: undefined, done: true}
```
调用Generator函数，返回一个遍历器对象，代表Generator函数的内部指针。以后,每次调用遍历器对象的next方法都会返回一个有着value和done两个属性的对象，value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值，done属性是一个布尔值，表示是否遍历结束。
##### yield表达式
由于Generator函数返回的是遍历器对象只有调用next方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数，yield表达式就是暂停标志。
需要注意的是，yield表达式后面的表达式，只有当调用next方法内部指针指向该语句时才会执行，因此等于为JavaScript提供了手动的"惰性求值"的语法功能

yield表达式与return语句有相似之处也有区别。相似之处在于都能返回紧跟在语句后面的那个表达式的值，区别在于每次遇到yield，函数暂停执行，下一次再从该位置继续向后执行，而return语句不具备位置记忆的功能。

Generator也可以不用yield表达式，这时就变成了一个单纯的暂缓执行函数。
```
    function* f() {
        console.log('我执行了!');
    }
    var generator = f(); // 这里不会执行
    setTimeout(function() {
        generator.next(); // 此时才会执行
    },2000)
```
需要注意的是，yield表达式只能用在Generator函数里面，用在其他地方会报错。


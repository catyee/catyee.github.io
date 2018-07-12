---
title: ES6学习之let和const命令
date: 2018-07-12 09:16:14
tags: ES6
categories: 前端学习笔记(ES6)
---

###### ECMAScript和JavaScript的关系
1996 年 11 月，JavaScript 的创造者 Netscape 公司，决定将 JavaScript 提交给标准化组织 ECMA，希望这种语言能够成为国际标准。次年，ECMA 发布 262 号标准文件（ECMA-262）的第一版，规定了浏览器脚本语言的标准，并将这种语言称为 ECMAScript，这个版本就是 1.0 版。
因此，ECMAScript和JavaScript的关系是前者是后者的规格，后者是前者的一种实现。日常场合，这两个词是可以互换的。
###### ES6与ECMAScript2015的关系
2011年，ECMAScript 5.1版发布后，就开始制定6.0版了，因此ES6这个词的原意，就是指JavaScript语言的下一个版本。
每年的6月份会发布一次标准，作为当年的正式版本。用年份来标记。ES6的第一个版本就是在2015年6月发布的，正式名称就是《ECMAScript 2015标准》（简称ES2015）
###### 浏览器支持
各大浏览器的最新版本，对ES6的支持可以查看kangax.github.io/es5-compat-table/es6/。可以使用**Babel转码器** 来将ES6代码转成ES5代码。

##### let命令
###### 基本用法
- ES6新增了let命令，用来声明变量，用法类似于var。
    ```
        {
            let a = 10;
            var b = 1;
        }
        console.log(a); // ReferenceError: a is not defined
        console.log(b); // 1
    ```
    如上所示，let声明的变量只在let命令所在的代码块内有效。


    ```
        var a = [];
        for(var i = 0; i < 10; i++) {
            a[i] = function() {
                console.log(i);
            }
        }
        a[6](); // 10
    ```
    在上述代码中，变量i是var声明的，在全局范围内都是有效的，所以全局就只有一个变量i。每一次循环变量i都会发生改变。所以最后循环结束后，输出的i值是10。

    ```
        var a = [];
        for(let i = 0; i < 10; i ++){
            a[i] = function() {
                console.log(i);
            }
        }
        a[6](); // 6
    ```
    在上述代码中i是用let声明的，所以每一次循环的i都是一个新的变量，所以最后输出的是6。 每一轮循环的i都是重新声明的，JavaScript引擎内部会记住上一轮循环的值
    另外，for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
    ```
        for(let i = 0; i < 3; i ++){
            let i = 'abc';
            console.log(i);
        }
        // 'abc'
        // 'abc'
        // 'abc'
    ```

######  注意的地方

- var命令存在变量提升的现象，即变量可以在声明之前使用，值为undefined。
  var a = 2; 首先声明一个变量a，然后初始化a为undefined，然后a赋值为2
  let a = 2;  声明 不进行初始化
- let 暂时性死区的本质就是只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用当前变量
- let不允许在相同作用域内重复声明同一个变量

##### ES6的块级作用域

- let实际上为JavaScript新增了块级作用域
```
    function f1() {
        let n = 5;
        if(true) {
            let n = 10;
        }
        console.log(n) // 5
    }
```
上面的函数有两个代码块，都声明了变量n最后输出5，这表示外层代码不受内层代码块的影响。


##### const命令
const声明一个只读的常量，一旦声明，常量的值就不能改变。
const只声明不赋值就会报错，声明只在所在的块级作用域内有效。不存在变量提升，存在暂时性死区，只能在声明的位置后面使用。
需要注意的是const声明的变量并不是要求变量的值不得改动，而是变量指向的那个内存地址不得改动，对于简单数据类型，值就保存在变量指向的那个内存地址，因此等同于常量。但对于引用数据类型，const只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了，因此，将一个对象声明为常量必须非常小心。
可以使用Object.freeze方法来冻结对象

##### ES6声明变量的六种方法
ES5只有两种声明变量的方法: var命令和function命令。ES6除了添加了let和const命令，还有import和class命令，所以ES6一共有六种声明变量的方法。
##### 顶层对象的属性
```
    var a = 1;
    window.a // 1

    let b = 1;
    window.b // undefined
```
let命令，const命令，class命令声明的全局变量，不属于顶层对象的属性。
---
title: Class的基本语法
date: 2018-07-16 17:16:37
tags: ES6
categories: 前端学习笔记(ES6)
---
##### 简介
在JavaScript中，生成实例对象的传统方法是通过构造函数。例如:
```
    function Point(x,y) {
        this.x = x;
        this.y = u;
    }
    Point.prototype.toString = function() {
        return '('+ this.x + ',' + this.y + ')';
    }
    var p = new Point(1,2);
```
ES6提供了更接近传统语言(如C++和Java)的写法，引入了Class(类)这个概念，作为对象的模板。通过class关键字，可以定义类。
基本上，ES6的class可以看作是一个语法糖，它的绝大部分功能，ES5都可以做到，新的class写法只是让对象原型的写法更加清晰，更像面向对象编程的语法而已。上面的代码可以用ES6的class改写如下:
```
    class Point {
        constructor(x,y) {
            this.x = x;
            this.y = y;
        }
        toString() {
            return '('+ this.x + ',' + this.y + ')';
        }
    }
```
上面代码定义了一个类，可以看到里面有一个constructor方法，这就是构造方法，而this关键字则代表实例对象，也就是说ES5的Point构造函数对应ES6的Point类的构造方法。
Point类除了构造方法，还定义了一个toString方法，注意:定义类的方法的时候，前面不需要加上function这样的关键字，直接把函数定义放进去就可以。另外方法之间不需要逗号分隔，加了会报错

```
    class Point {
        // ...
    }
    typeof Point; // "function"
    Point == Point.prototype.constructor; // true
```
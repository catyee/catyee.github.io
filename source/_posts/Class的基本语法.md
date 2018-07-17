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
上面的代码表明，类的数据类型就是函数，类本身指向构造函数。
使用的时候，也是直接对类使用new命令。
**需要注意的是类的内部定义的方法，都是不可枚举的。这一点ES5的行为不一致。**
##### 严格模式
类和模块的内部，默认就是严格模式，所以不需要use strict指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。
##### constructor方法
constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显示定义，一个空的constructor方法就会被默认添加。
constructor方法默认返回实例对象(即this)，完全可以指定返回另一个对象。
```
    class Foo {
        constructor() {
            return Object.create(null);
        }
    }
    new Foo() instanceof Foo(); // false
```
##### 类的实例对象
生成类的实例对象的写法，与ES5完全一样，也是使用new命令，如果忘记加上new命令，像函数那样调用Class，将会报错。
与ES5一样，所有的实例共享一个原型对象。
##### Class表达式
与函数一样，类也可以使用表达式的形式定义。
```
    const Myclass = class Me {
        getClassName() {
            return Me.name;
        }
    }
```
注意: 上面代码中类的名字是Myclass而不是Me，Me只在class的内部代码可用，指代当前类。

##### 不存在变量提升
类不存在变量提升，这一点与ES5完全不同。
```
    new Foo(); // error
    class Foo{}
```
上面代码中，Foo类使用在前，定义在后，这样会报错，因为ES6不会把类的声明提升到代码头部。这种规定的原因与下文要提到的继承有关，必须保证子类在父类之后定义。

```
    let Foo = class {}；
    class Bar extend Foo {

    }
```
上面的代码不会报错，因为Bar继承Foo的时候，Foo已经有了定义了，但是，如果存在class的提升，则会报错，因为class会被提升到代码头部，而let命令是不提升的，所以导致Bar继承Foo的时候，Foo还没有定义。


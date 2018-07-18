---
title: Decorator
date: 2018-07-16 17:17:22
tags: ES6
categories: 前端学习笔记(ES6)
---
##### 类的修饰
修饰器(Decorator)用来修改类的行为。
```
    @testable
    class MyTestableClass {
        // ...
    }
    function testable(target) {
        target.isTestable = true;
    }
    MyTestableClass.isTestable // true
```
上面代码中，@testable就是一个修饰器。它修改了MyTestableClass这个类的行为，为它加上了 **静态属性**isTestable。testable函数的参数target是MyTestableClass类本身。
基本上，修饰器的行为就是下面这样。
```
    @decorator
    class A {}
    // 等同于
    class A {}
    A = decorator(A) || A;
```
也就是说，修饰器是一个对类进行处理的函数。修饰器函数的第一个参数就是所要修饰的目标类。
如果觉得一个参数不够用，可以在修饰器外面再封装一层函数
```
    function testable(isTestable) {
        return function(target) {
            target.isTestable = isTestable;
        }
    }
    @testable(true) // 相当于testable(MyTestableClass)(true)
    class MyTestableClass {
        // ...
    }
```
##### 方法的修饰
修饰器不仅可以修饰类，还可以修饰类的属性。
```
    class Person {
        @readonly
        name() {
            return `${this.first} ${this.last}`
        }
    }
```
上面代码，修饰器readonly用来修饰类的name方法。
修饰器函数readonly一共可以接受三个参数
```
    function readonly(target,name,descriptor) {
        // descriptor对象原来的值如下
        // {
        //   value: specifiedFunction,
        //   enumerable: false,
        //   configurable: true,
        //   writable: true
        // };
        descriptor.writable = false;
        return descriptor;
    }
    readonly(Person.prototype, 'name', descriptor);
    // 类似于
    Object.defineProperty(Person.prototype, 'name', descriptor)
```
修饰器有注释的作用，通过修饰器可以很清楚的看到函数的功能及性质。
如果一个方法有多个修饰器，就会从外到内进入，然后从内到外执行。
##### 修饰器不能用于函数
修饰器只能用于类和类的方法，不能用于函数，因为存在变量提升。

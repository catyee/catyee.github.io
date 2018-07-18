---
title: Class的继承
date: 2018-07-16 17:16:56
tags: ES6
categories: 前端学习笔记(ES6)
---
##### 简介
Class可以通过extends关键字实现继承。
```
    clss Point {

    }
    class ColorPoint extends Point {
        constructor(x,y,color) { 
            super(x,y); // 调用父类的constructor(x,y)
            this.color = color;
        }
        toString() {
            return this.color + ' ' + super.toString(); // 调用父类的toString()
        }
    }
```
上面的代码中，constructor方法和toString方法中都出现了super关键字，它在这里表示父类的构造函数，用来新建父类的this对象。
**子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的属性和方法。如果不调用super方法，子类就得不到this对象。

```
    class Point {

    }
    class ColorPoint extends Point {
        constructor() {

        }
    }
    let cp = new ColorPoint(); // error
```
上面代码中，ColorPoint继承了父类Point，但是它的构造函数没有调用super方法，导致新建实例时报错。
ES5的继承，实质是先创造子类的实例对象this，然后再将父类的的方法添加到this上面(Parent.call/apply(this))。ES6的继承机制完全不同，实质是先将父类实例对象的属性和方法加到this上面，然后再用子类的构造函数修改this。

子类如果没有定义constructor方法，这个方法就会默认添加，并且默认添加super方法。
在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。这是因为子类实例的构建基于父类实例，只有super方法才能调用父类实例。
父类的静态方法，也会被子类继承。

**Object.getPrototypeOf()方法可以用来从子类上获取父类。因此，可以使用这个方法判断，一个类是否继承了另一个类**
##### super关键字
super这个关键字，既可以当作函数使用，也可以当作对象使用。这两种情况下，它的用法完全不同。
第一种情况，super作为函数调用时，代表父类的构造函数，ES6要求，**子类的构造函数必须执行一次super函数**
```
    class A {}
    class B extends A {
        constructor () {
            super();
        }
    }
```
在上面的代码中，子类B的构造函数之中的super(),代表调用父类的构造函数。这是必须的，否则JavaScript引擎会报错。
**注意:super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B，因此super()在这里相当于A.prototype.constructor.call(this)。**
作为函数式时，super()只能用在子类的构造函数中，用在其他地方会报错。

第二种情况，super作为对象时，在普通方法中，指向父类的原型对象，在静态方法中，指向父类。
```
    class A {
        p() {
            return 2;
        }
    }
    class B extends A {
        constructor() {
            super();
            console.log(super.p()); // 2 super.p()相当于A.prototype.p()
        }
    }
    let B = new B();
```
**需要注意的是，由于super指向父类的原型对象，所以定义在父类的实例上的方法或属性是无法通过super调用的。**

例如:
```
    class A {
        constructor() {
            this.p = 2;
        }
    }
    class B extends A {
       get m() {
           return super.p;
       }
    }
    let b = new B();
    console.log(b.m); // undefined
```

ES6规定，在子类普通函数中通过super调用父类的方法时，方法内部的this指向当前的子类实例。
在子类的静态方法中通过super调用父类的方法时，方法内部的this指向当前的子类，而不是子类的实例。

##### 勒的prototype属性和__proto__属性
大多数浏览器的ES5实现中，每一个对象都有__proto__属性，指向对应的构造函数的prototype属性。Class作为构造函数的语法糖，同时有prototype属性和__proto__属性。因此存在两条继承链。
- 子类的__proto__属性表示构造函数的继承，总是指向父类。
- 子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性。
```
    class A {

    }
    class B extends A {

    }
    B.__proto__ === A; // true
    B.prototype.__proto__ === A.prototype; // true
```
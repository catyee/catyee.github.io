---
title: Class的继承
date: 2018-07-16 17:16:56
tags:
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
第一种情况，super作为函数调用时，代表父类的构造函数，

---
title: javscript中的对象属性
date: 2018-06-25 16:58:51
tags: 'js'
---

### 属性类型
 - 我们可以用以下两种方式来创建一个对象:
 ```
    // 创建一个Object的实例
    var person = new Object();
    person.name = 'xiaohua';
    person.age = 20;
    person.sayname = function() {
        alert(this.name);
    }

    // 对象字面量
    var person = {
        name: 'xiaohua',
        age: 20,
        sayname: function() {
            alert(this.name)
        }
    }

 ```
 以上创建的这两个对象是一样的，都有相同的属性和方法，这些属性在创建时都带有一些特征值，javascript通过这些特征值来定义他们的行为。

 - ECMA-262定义这些特性是为了实现javascript引擎用的，因此在javascript中不能直接访问它们。为了表示特性是内部值，该规范把它们放在两对方括号中，例如[[Enumerable]]

 - ECAMAScript中有两种属性: 数据属性和访问器属性
    1.  数据属性 有四个描述其行为的特性
        - [[Configurable]]: 表示能通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能够把属性修改为访问器属性。前面例子中定义的对象的属性，这个特性默认值是true
        - [[Enumerable]]: 表示能否通过for-in循环返回属性。前面例子中定义对象的这个特性默认是true
        - [[Writable]]: 表示能够修改属性的值。前面例子中定义对象的这个特性默认是true
        - [[Value]]: 包含这个属性的数据值。读取属性值的时候，从这个位置读，写入属性的时候，将新值保存在这个位置。这个特性的默认值是undefined

        像前面例子中那样直接在对象中定义的属性，它们的[[Configurable]]，[[Enumerable]]，[[Writable]]特性都被设置为true，而[[Value]]特性被设置为指定的值。

        要修改属性默认的特性。必须使用Object.definedProperty()方法，这个方法接收三个参数，属性所在的对象，属性的名字和一个描述符对象，描述符对象必须为configurable，enumerable，writable 和value。设置其中的一个或多个值可以修改对应的特性值。
        例如:

        ```
            var person = {}; 
            Object.defineProperty(person, "name", {
                configurable: false,
                value: 'xiaohua'
            })
            alert(person.name); // 'xiaohua'
            delete person.name;
            alert(person.name); // xiaohua

        ```
        configurable设置为false表示不能从对象删除该属性，非严格模式下执行delete操作不会发生什么，而在严格模式下，会导致错误。

        **需要注意的是: 可以多次调用Object.definedProperty()方法修改同一属性，但是如果把configurable设置为false了，再调用Object.definedProperty()方法修改除了writable之外的特性都会报错。并且writable只能修改为false，否则也会报错。当writable为true时，即使configurable为false,value也可以修改。**

    2. 访问器属性 同样具有四个特性
        - [[Congigurable]]: 表示能通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能够把属性修改为访问器属性。前面例子中定义的对象的属性，这个特性默认值是true
        - [[Enumerable]]: 表示能否通过for-in循环返回属性。前面例子中定义对象的这个特性默认是true
        - [[Get]]: 在读取属性时调用的函数。默认值是undefined
        - [[Set]]: 在写入属性时调用的函数。默认值是undefined

    访问器属性不能直接定义，必须使用Object.defineProperty()来定义。例如:
    ```
        var book = {
            _year: 2004,
            edition: 1
        };
        Object.defineProperty(book, 'year', {
            get: function() {
                return this._year;
            },
            set: function(newValue) {
                if(newValue > 2004) {
                    this._year = newValue;
                    this.edition += newValue - 2004
                }
            }
        })
        book.year = 2005;
        alert(book.edition); // 2
    ```
    以上代码创建了一个book对象，并给它定义两个默认的属性: _year和edition。_year前面的下划线是一种常用的几号，用于表示只能通过对象方法访问的属性。而访问器属性year则包含一个getter函数和一个setter
    函数。我们将year值修改为2005后，setter方法将_year变成了2005，edition变为2。这是使用访问器属性的常见方式，即设置一个属性的值会导致其他属性发生变化。
### 定义多个属性
    使用Object.defineProperties()方法可以用来一次定义多个属性的特性。这个方法接收两个对象参数，第一个对象是要添加和修改其属性的对象，第二个是要设置的属性对应的特性值对象。如下所示:
    
    ```
        var book = {};
            Object.defineProperties(book, {
                _year: {
                    value: 2004
                },
                edition: {
                    value: 1
                },
                year: {
                            get: function() {
                    return this._year;
                },
                set: function(newValue) {
                    if(newValue > 2004) {
                        this._year = newValue;
                        this.edition += newValue - 2004
                    }
                }
                }
            })

    ```
   
    以上代码在book对象上定义了两个数据属性(_year和edition)和一个访问器属性(year)

### 读取属性的特性
    使用Object.getOwnPropertyDescriptor()方法可以取得给定属性的描述符。接收两个参数:属性所在的对象和要读取其描述符的属性名称，返回值是一个对象。例如:

    ``` 
        var book = {};
        Object.defineProperties(book, {
            _year: {
                value: 2004
            },
            edition: {
                value: 1
            },
            year: {
                get: function() {
                    return this._year;
                },
                set: function(newValue) {
                    if(newValue > 2004) {
                        this._year = newValue;
                        this.edition += newValue - 2004
                    }
                }
            }
        })
        var descriptor = Object.getOwnPropertyDescriptor(book, "_year"); 
        alert(descriptor.value) // 2004
        alert(descriptor.configurable) // false
        alert(typeof descriptor.get); //"undefined" 
        var descriptor = Object.getOwnPropertyDescriptor(book, "year"); 
        alert(descriptor.value) // undefined
        alert(descriptor.enumerable); //false 
        alert(typeof descriptor.get); //"function" 
    ```
        对于数据属性_year,value等于最初的值，configurable是false，get是undefined。对于访问器属性year，value等于undefined，enumrable是false，而get是一个指向getter函数的指针
        

   
    
> **主要参考: JavaScript高级程序设计(第3版)**
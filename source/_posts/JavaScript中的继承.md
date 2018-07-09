---
title: JavaScript中的继承
date: 2018-07-09 10:19:31
tags: js
---

继承是OO语言中的一个最为人津津乐道的概念。许多OO语言都支持两种继承方式:接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。函数没有前面，在ECMAScript中无法实现接口继承。ECMAScript只支持实现继承，而且其实现继承主要是依靠原型链来实现的。

## 原型链
ECMAScript中描述了原型链的概念，并将原型链作为实现继承的主要方法。其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。简单回顾一下构造函数、原型和实例的关系: 每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。那么，假如我们让原型对象等于另一个类型的实例，那么结果会怎么样呢？显然，此时的原型对象将包含一个指向另一个原型的指针（__proto__）,相应地，另一个原型中也包含着一个指向另一个构造函数的指针(constructor)。假设另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了实例与原型的链条。这就是原型链的基本改变。

实现原型链有一种基本模式，其代码大致如下:
```
    function SuperType() {
        this.property = true;
    }
    SuperType.prototype.getSuperValue = function() {
        return this.property;
    }
    function SubType() {
        this.subproperty = false;
    }
    // 继承了SuperType
    SubType.prototype = new SuperType();
    SubType.prototype.getSubValue = function() {
        return this.subproperty;
    }
    var instance = new SubType();
    alert(instance.getSuperValue()); // true

    实现了Subtype继承SuperType，实现的本质就是重写了原型对象，代之以一个新类型的实例

    调用instance.getSuperValue()会经历三个搜索步骤: 1) 搜索实例 2) 搜索SubType.prototype 3) 搜索SuperType.prototype,最后一步才会找到该方法。在找不到属性或方法的情况下，搜索过程总是要一环一环地前行到原型链末端才会停下来。
```
### 不要忘记默认的原型
 事实上，前面的例子中展示的原型链还少一环。我们知道，所有的引用类型默认都继承了Object，而这个继承也是通过原型链实现的。**所有函数的默认原型都是Object的实例，因此默认原型都会包含一个内部指针指向Object.prototype,这也正是所有自定义类型都会继承toString(),valueOf()等默认方法的根本原因**
### 确定原型和实例的关系
 可以通过两种方式来确定原型和实例之间的关系。第一种方式是使用instanceof操作符，只要用这个操作符来测试实例与原型链中出现过的构造函数，结果就会返回true。
 ```
 alert(instance instanceof Object); // true
 alert(instance instanceof SuperType); // true
 alert(instance instanceof SubType); // true
 ```
 由于原型链的关系，我们可以说instance是Object、SuperType或SubType中任何一个类型的实例。因此，测试这三个构造函数的结果都返回了true。

 第二种方式是使用isPrototypeOf()方法。同样，只要是原型链中出现过的原型，都可以说是该原型链所派生的实例的原型，因此isPrototypeOf()方法也会返回true
 ```
    alert(Object.prototype.isPrototypeOf(instance)); // true
    alert(SuperType.prototype.isPrototypeOf(instance)); // true
    alert(subType.prototype.isPrototypeOf(instance)); // true
 ```
### 谨慎地定义方法
 子类型有时候需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法。但不管怎样，**给原型添加方法的代码一定要放在替换原型的语句之后**
 ```
    function SuperType() {
        this.property = true;
    }
    SuperType.prototype.getSuperValue = function() {
        return this.property;
    }
    function SubType() {
        this.subproperty = false;
    }
    // 继承了SuperType
    SubType.prototype = new SuperType();
    // 添加新方法
    SubType.prototype.getSubValue = function() {
        return this.subproperty;
    }
    // 重写超类型中的方法
    SubType.prototype.getSuperValue = function() {
        return false;
    }
    var instance = new SubType();
    alert(instance.getSuperValue()); // false
 ```
 在以上代码中，添加了一个方法getSubValue(),重写了原型链中已经存在的方法getSubValue(),但重写这个方法将会屏蔽原来的那个方法，即当通过SubType的实例调用getSuperValue()时，调用的就是这个重新定义的方法;但通过SuperType的实例调用getSuperValue()时，还会继续调用原来的那个方法。这里需要格外注意的是，必须在用SuperValue的实例替换原型后，再定义这两个方法。
 还有一点要注意的是 **在通过原型链实现继承时，不能使用对象字面量创建原型方法，因为这样会重写原型链**
 ```
    function SuperType() {
        this.property = true;
    }
    SuperType.property.getSuperValue = function() {
        return this.property;
    }
    function SubType() {
        this.subProperty = false;
    }
    // 继承了SuperType
    SubType.protoType = new SuperType();
    // 使用字面量添加新方法，会导致上一行代码无效
    SubType.prototype = {
        getSubValue: function() {
            return this.subproperty;
        },

    };
    var instance = new SubType();
    alert(instance.getSuperValue()); // error
 ```
### 原型链的问题
 原型链虽然很强大，可以用它来实现继承，但它也存在一些问题，其中最主要的问题来自 **包含引用类型值的原型** 。之前介绍过，在用原型模式创建对象时，原型中的所有属性是被所有实例共享的，如果原型中包含的属性是引用类型的值，在实例中修改这个属性会影响其他的实例。所以我们不再原型对象中定义属性，而是在构造函数中定义。
 ```
    function SuperType() {
        this.colors = ['red', 'blue', 'green'];
    }
    function SubType() {

    }
    // 继承了SuperType
    SubType.prototype = new SuperType();
    var instance1 = new SubType();
    instance1.colors.push('black');
    alert(instance1.colors); // ['red', 'blue', 'green','black']

    var instance2 = new SubType();
    alert(instance2.colors); // ['red', 'blue', 'green','black']

    var instance3 = new SuperType();
    alert(instance3.colors); // ['red', 'blue', 'green']
 ```
 这个例子中SuperType构造函数定义的color属性是定义在构造函数中的，所以它的每个实例都会有各自包含自己数组的colors属性，不会相互影响。但是当SubType通过原型链继承了SuperType之后，SubType.prototype就变成了SuperType的一个实例，因此它也拥有了一个自己的colors属性————相当于创建了一个SubType.prototype.colors属性一样。所以SubType的所有实例都会共享这个一个colors属性。

 原型链的第二个问题是在创建子类型的实例时，不能向超类型的构造函数中传递参数。实际上应该说是没办法在不影响所有对象实例的情况下，给超类型的构造函数传递参数，因此实践中很少会单独使用原型链。

 ## 借用构造函数
 在解决原型中包含引用类型值所带来的问题的过程中，开发人员开始使用一种叫做借用构造函数的技术（有时候也叫做伪构造对象或经典继承）。这种技术的基本思想相当简单，即子类型构造函数的内部调用超类型构造函数，别忘了，函数只不过是在特定环境中执行代码的对象，因此通过apply()和call()方法可以在（将来）新创建的对象上执行构造函数。


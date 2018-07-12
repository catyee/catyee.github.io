---
title: JavaScript中的继承
date: 2018-07-09 10:19:31
tags: JavaScript
categories: 前端学习笔记(JavaScript)
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
 ```
    function SuperType() {
        this.colors = ['red','blue','green'];
    }
    function SubType() {
        // 继承了SuperType
        SuperType.call(this);
    }
    var instance1 = new SubType();
    instance1.colors.push('black');
    alert(instance1.colors); // ['red','blue','green','black']
    var instance2 = new SubType();
    alert(instance2.colors); // ['red','blue','green']
 ```
上面的代码使用call()方法(或apply()方法也可以)，实际上是在（未来将要）新创建的SubType实例的环境下调用了SuperType构造函数。这样一来，就会在新SubType对象上执行SuperType()函数中定义的所有对象初始化代码。结果,SuperType的每个实例就都会具有自己的colors属性的副本了。

- 相对于原型链而言，借用构造函数有一个很大的优势，即可以在子类型构造函数中向超类型构造函数传递参数。
```
    function SuperType(name) {
        this.name = name;
    }
    function SubType() {
        // 继承了SuperType，同时还传递了个参数
        SuperType.call(this, 'xiaohua');
        this.age = 20;
    }
    var instance = new SubType();
    alert(instance.name); // 'xiaohua'
    alert(instance.age); // 20

```
- 如果仅仅是借用构造函数，那么也就无法避免构造函数存在的问题————方法都在构造函数中定义，因此函数复用就无从谈起了。而且在超类型的原型中定义的方法，对于子类型也是不可见的，结果所有类型都只能使用构造函数模式。因此借用构造函数的模式也是很少单独使用的。
## 组合继承
组合继承有时候也叫伪经典继承，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能保证每个实例都有它自己的属性。
```
    function SuperType(name){
        this.name = name;
        this.colors = ['red', 'blue', 'green'];
    }
    SuperType.prototype.sayName = function() {
        alert(this.name);
    }
    function SubType(name,age) {
        //继承属性
        SuperType.call(this, name);
        this.age = age;
    }
    // 继承方法
    SubType.prototype = new SuperType();
    SubType.prototype.constructor = SubType; // 否则为SuperType
    SuperType.prototype.sayAge = function() {
        alert(this.age);
    }
    var instance1 = new SubType('huahua',20);
    instance1.colors.push('black');
    alert(instance1.colors); // ['red', 'blue', 'green','black']
    instance1.sayName(); // 'huahua'
    instance1.sayAge(); // 20

    var instance2 = new SubType('xiaohei',22);
    alert(instance2.colors); // ['red', 'blue', 'green']
    instance2.sayName(); // 'xiaohei'
    instance2.sayAge(); // 22


```
instanceof和isPrototypeOf() 也能够识别给予组合继承创建的对象

## 原型式继承
先看下面这个函数
```
    function object(o) {
        function F() {};
        F.prototype = o;
        return new F(); 
    }
```
在object函数内部，先创建了一个临时性的构造函数，然后将传入的对象作为这个构造函数的原型，最后返回这个临时类型的一个新实例。 从本质上将object对传入其中的对象执行了一次浅复制。
ECMAScript5通过新增Object.create()方法规范了原型式继承，这个方法接收两个参数，一个用作新对象原型的对象和一个为新对象定义额外属性的对象（可选的），在传入一个参数的情况下，Object.create()和object()方法的行为相同。
```
    var person = {
        name: 'Q',
        friends: ['huahua','xiaohei','mew']
    };
    var anotherPerson = Object.create(person);
    anotherPerson.name = 'tomato';
    anotherPerson.friends.push('potato');
    var yetAnotherPerson = Object.create(person);
    alert(person.friends); // ['huahua','xiaohei','mew','potato']
```
Object.create()方法的第二个参数与Object.defineProperties()方法的第二个参数格式相同: 每个属性都是通过自己的描述符定义的。以这种方式指定的任何属性都会覆盖原型对象上的同名属性，例如:
```
    var person = {
        name: 'Q',
        friends: ['huahua','xiaohei','mew']
    };
    var anotherPerson = Object.create(person, {
        name: {
            value: 'tomato'
        }
    })
    alert(anotherPerson.name); // 'tomato'
```
在没有必要创建构造函数而只想让一个对象与另一个对象保持类似的情况下，原型式继承是完全可以胜任的。不过别忘了，包含引用类型值的属性始终都会共享相应的值，就像使用原型模式一样。
## 寄生式继承
寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在卑不以某种方式来增强对象，最后再像真的是它做了所有工作一样返回对象。
```
    function object(o) {
        function F() {};
        F.prototype = o;
        return new F(); 
    }

    function createAnother(original) {
        var clone = object(original); // 通过调用函数创建一个新对象
        clone.sayHi = function() { // 以某种方式增强这个对象
            alert('hi');
        }
        return clone;
    }
```
在上面这个例子中，createAnothe()函数接收了一个参数，也就是将要作为新对象基础的对象。然后，把这个对象（original）传递给object()函数，将返回的结果赋值给clone，再为clone对象添加一个新方法sayHi(),最后返回clone对象，可以像下面这样来使用createAnother()函数。
```
    var person = {
        name: 'Q',
        friends: ['huahua','xiaohei','mew']
    };
    var anotherPerson = createAnother(person);
    anotherPerson.sayHi(); // hi
```
createAnother()创建的新对象不仅具有person的所有属性和方法，而且还有自己的sayHi方法
**在主要考虑对象而不是自定义类型和构造函数的情况下，寄生式继承也是一种有用的模式。不仅可以用object()函数，任何能够返回新对象的函数都适用于此模式。使用寄生式继承来为对象添加函数，会由于不能做到函数复用而降低效率，这一点与构造函数模式类似**

## 寄生组合式继承
前面说过，组合继承是JavaScript中最常用的继承方式，不过它也有自己的不足。组合继承最大的问题就是无论什么情况下，都会调用两次超类型构造函数。一次是在创建子类型原型的时候，另一次是在子类型构造函数的内部。没错，子类型最终会包含超类型对象的全部实例属性，但我们不得不在调用子类型构造函数时重写这些属性
```
     function SuperType(name){
        this.name = name;
        this.colors = ['red', 'blue', 'green'];
    }
    SuperType.prototype.sayName = function() {
        alert(this.name);
    }
    function SubType(name,age) {
        //继承属性
        SuperType.call(this, name); // 第二次调用SuperType()
        this.age = age;
    }
    // 继承方法
    SubType.prototype = new SuperType(); 
    SubType.prototype.constructor = SubType;  // 第一次调用SuperType()
    SuperType.prototype.sayAge = function() {
        alert(this.age);
    }

```
寄生组合式继承可以解决上述问题。所谓寄生组合式继承，即通过借用构造函数来继承对象，通过原型链的混成形式来继承方法。其背后的思路是: 不必为了指定子类型的原型而调用超类型的构造函数，我们所需的无非就是超类型原型的一个副本而已，本质上，使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型
```
    function object(o) {
        function F() {};
        F.prototype = o;
        return new F(); 
    }
    function inheritProrotype(subType,superType) {
        var prototype = object(superType);
        prototype.constructor = subType();
        subType.prototype = prototype;
    }
    function SuperType(name){
        this.name = name;
        this.colors = ['red', 'blue', 'green'];
    }
    SuperType.prototype.sayName = function() {
        alert(this.name);
    }
    function SubType(name,age) {
        SuperType.call(this, name); // 第二次调用SuperType()
        this.age = age;
    }
    inheritProrotype(subType,superType);
    SuperType.prototype.sayAge = function() {
        alert(this.age);
    }
```
这个例子的高效体现在它只调用了一次SuperType构造函数，并且因此避免了在SubType.prototype上面创建不必要的、多余的属性。与此同时，原型链还能保持不变，因此，还能够正常使用instanceof和isPrototypeOf()。普遍认为这是最理想的继承范式。
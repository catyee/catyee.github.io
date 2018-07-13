---
title: ES6学习之Promise
date: 2018-07-12 14:52:58
tags: ES6
categories: 前端学习笔记(ES6)
---

##### Promise的含义
Promise是异步编程的一种解决方案，比传统的解决方案————回掉函数和事件———— 更合理和更强大。
所谓Promise,简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上讲，Promise是一个对象，从它可以获取异步操作的消息。Promise提供统一的API，各种异步操作都可以用同样的方法来处理。

Promise对象的特点:
1. 状态不受外界影响。作为一个异步操作，有三种状态: pending(进行中)，fulfilled(已成功)和rejected(已失败)。只有异步操作的结果可以决定当前是哪一种状态，任何其他操作都无法改变。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。 状态改变只有两种可能: 从pending变为fulfilled和从pending变为rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就成为resolve(已定型)。如果改变已经发生了，你再对Promise对象添加回调函数，也会立即得到这个结果。这与事件完全不同，事件的特点是如果你错过了它，再去监听，是得不到结果的。

有了promise对象，就可以将异步操作以同步操作的流程表达出来，避免了层曾嵌套的回调函数。此外，Promise对象提供统一的接口，使得控制异步操作更加容易。

- Promise的缺点: 首先，无法取消Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise内部抛出的错误不会反应到外部。第三，当处于pending状态时，无法得知目前进展到哪一个阶段(刚刚开始还是即将完成)。

##### 基本用法
```
    const promise = new Promise(function(resolve,reject) {
        // ..some code
        if(/* 异步操作成功 */) {
            resolve(value);
        }else {
            reject(error);
        }
    });
```

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。

resolve函数的作用是将Promise对象的状态从"未完成"变为"成功"(即从pending变为resolved)，在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
reject函数的作用是将Promise对象的状态从"未完成"变为"失败"(即从pending变为rejected)，在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

Promise实例生成以后，可以使用then方法分别指定resolved状态和rejected状态的回掉函数
```
    promise.then(function(value) {
        // success
    }, function(error) {
        // failure
    })
```
then方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为resolved时调用，第二个回调函数是Promise对象的状态变为rejected时调用。 其中第二个函数是可选的

**一般来说，调用resolve或reject以后，Promise的使命就完成了，后续操作应该放到then方法里面，而不应该写在resolve或reject的后面，所以，最好在它们前面加上return语句，这样就不会有意外**

##### Promise.prototype.then
then方法返回的是一个新的Promise实例(注意，不是原来那个Promise实例)。因此可以采用链式写法，即then方法后面再调用另一个then方法。
```
    getJson("posts.json").then(function(json) {
        return json.post;
    }).then(function(post) {
        // ...
    })
```
在上述代码中，第一个then方法的回调执行完成以后会将结果作为参数传入第二个then方法的回调

前一个回调也可能返回的是一个Promise对象（即有异步操作），这时后一个回调函数就会等待该Promise对象的状态发生改变，才会调用
```
    getJson("posts.json").then(function(json) {
        return getJSON(post.commentUrl);
    }).then(function(comments) {
        console.log("resolved:", comments);
    },function(err) {
        console.log("rejected:", err)
    })
```
##### Promise.prototype.catch
Promise.prototype.catch是 .then(null,rejection)的别名，用于指定发生错误时的回调函数。
```
    getJson("posts.json").then(function(json) {
        return json.post;
    }).catch(function(error) {
        // 错误处理
    })
```
上述代码中，如果异步操作抛出错误，状态就会变为rejected，就会调用catch方法指定的回调函数处理这个错误，另外then方法指定的回调函数如果在运行中抛出错误，也会被catch捕获。

**一般来书，不要在then方法里面定义reject状态的回调函数(即then的第二个参数)总是使用catch方法，理由是catch方法可以捕获前面then方法执行中的错误，也更接近同步的写法（try/catch）**

跟传统的try/catch代码不同的是，如果没有使用catch方法指定错误处理的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应。
catch方法返回的还是一个Promise对象，所以后面还是可以接一个then方法，如果没有出现错误，会跳过catch方法，执行then方法。

##### Promise.prototype.finally()
finally方法用于指定不管Promise对象最后状态如何，都会执行的操作。该方法是ES2018引入标准的。
```
    promise.then(result => {})
           .catch(error => {})
           .finally(() => {})
```
finally方法的回调函数不接受任何参数，finally方法里面的操作，是与Promise的状态无关的，不依赖于Promise的执行结果

##### Promise.all
Promise.all方法用于将多个Promise实例，包装成一个新的Promise实例
```
    const p = Promise.all([p1,p2,p3]);
```
上述代码中，Promise.all方法接受一个数组作为参数，p1,p2,p3都是Promise实例，如果不是，就会调用下面讲到的Promise.resolve方法将参数转为Promise实例，再进一步处理（Promise.all方法的参数可以不是数组，但必须具有Iterator接口，且返回的每个成员都是Promise实例）。
p的状态由p1,p2,p3决定，分为两种情况
1) 只有p1, p2, p3的状态都变成fulfilled, p的状态才会变成fulfiled，此时p1, p2, p3 的返回值组成一个数组，传递给p的回调函数。
2) 只要p1, p2, p3 之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值就会传递给p的回调函数。

##### Promise.race()
Promise.race方法同样是将多个Promise实例，包装成一个新的Promise实例
```
    const p = Promise.race([p1,p2,p3]);
```
上面的代码中，只要p1, p2, p3之中有一个实例率先改变状态，p的状态就跟着改变，那个率先改变的Promise实例的返回值，就传递给p的回调函数
Promise.race()方法的参数与Promise.all()方法一样，如果不是Promise实例，就会先调用下面讲到的Promise.resolve方法，将参数转为Promise实例，再进一步处理。

##### Promise.resolve()
有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用
```
    Promise.resolve('foo');
    // 等价于
    new Promise(resolve => resolve('foo'));
```
Promise.resolve方法的参数分成四种情况

(1) 参数是一个Promise实例
如果参数是Promise实例，那么Promise.resolve将不做任何修改，原封不动地返回这个实例。
(2) 参数是一个thenable对象
thenable对象指的是具有then()方法的对象，比如下面这个对象
```
    let thenable = {
        then: function(resolve, reject) {
            resolve(42);
        }
    }
```
Promise.resolve方法会将这个对象转为Promise对象，然后立即执行thenable对象的then方法。
```
    let thenable = {
        then: function(resolve, reject) {
            resolve(42);
        } 
    }
    let p1 = Promise.resolve(thenable);
    p1.then(function(value) {
        console.log(value); // 42
    })
    上面代码中，thenable对象的then方法执行后，对象p1的状态就变为resolved，从而立即执行最后那个then方法指定的回调函数，输出42
```
(3) 参数不是具有then方法的对象，或根本就不是对象
 如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的Promise对象，状态为resolved

 ```
    const p = Promise.resolve('Hello');
    p.then(function(s) {
        console.log(s);
    })
    // Hello
 ```
上面代码生成一个新的Promise对象的实例p，由于字符串Hello不属于异步操作(判断方法是字符串对象不具有then方法),返回Promise实例的状态从一生成就是resolved，所以回调函数会立即执行，Promise.resolve方法的参数，会同时传给回调函数。

(4) 不带任何参数
Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的Promise对象
所以，如果希望得到一个Promise对象，比较方便的方法就是直接调用Promise.resolve方法
```
    const p = Promise.resolve();
    p.then(function() {
        //...
    })
```
需要注意的是，立即resolve的Promise对象，是在本轮"事件循环"的结束时，而不是在下一轮"事件循环"的开始时
```
    setTimeout(function() {
        console.log("three");
    },0)
    Promise.resolve().then(function() {
        console.log("two");
    })
    console.log("one");
    // one
    // two
    // three
```
上面代码中，setTimeout(fn,0)在下一轮"事件循环"开始时执行，Promise.resolve()在本轮"事件循环"结束时执行。

##### Promise.reject()
Promise.reject(reason)方法也会返回一个新的Promise实例，该实例的状态为rejected
##### Promise.try()
实际开发中，经常遇到一种情况:不知道或者不想区分，函数f是同步函数还是异步操作，但是想用Promise来处理它。因为这样就可以不管f是否包含异步操作，都用then方法指定下一步流程，用catch方法处理f抛出的错误。一般就会采用下面的写法:
```
   Promise.resolve().then(f) 
```
上面的写法有个缺点，就是如果f是同步函数，那么它就会在本轮事件循环的末尾执行。

```
    const f = () => console.log('now');
    Promise.resolve().then(f);
    console.log('next');
    // next
    // now
```
上面的代码中，函数f是同步的，但是用Promise包装了以后，就变成异步执行了。
要让同步函数同步执行，异步函数异步执行，我们可以使用两种方法来实现。

第一种是async写法:
```
    const f = () => console.log('now');
    (async () => f())();
    console.log('next');
    // now
    // next
```
上面代码中，第二行是一个立即执行的匿名函数，会立即执行里面的async函数，因此如果f是同步的，就会得到同步的结果，如果f是异步的就可以用then指定下一步，就像下面的写法
```
    (async () => f())()
    .then(...)
    // 因为async () => f() 会吃掉f()抛出的错误，所以必须用promise.catch来捕获错误
    .catch(...)
```
第二种写法是使用new Promise()
```
    const f = () => console.log('now');
    (
        () => new Promise(
            resolve => resolve(f())
        )
    )();
    console.log('next');
    // now
    // next
```
可以使用Promise.try来替代上面的写法
```
    Promise.try(getId('posts.json'))
           .then(...)
           .catch(...)
```




---
title: async函数
date: 2018-07-16 09:23:13
tags: ES6
categories: 前端学习笔记(ES6)
---
##### 含义
async函数就是Generator函数的语法糖。
async函数对Generator函数进行了改进:
(1) 内置执行器
Generator函数的执行必须依靠执行器，所以才有了co模块，而async函数自带执行器。也就是说，async函数的执行与普通函数一摸一样，只要一行。
```
    f();
```
(2) 更好的语义
async和await，比起星号和yield，语义更加清楚，async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。
(3) 更广的适用性
co模块约定，yield命令后面只能是Thunk函数或Promise对象，而async函数的await命令后面，可以是Promise对象和原始类型的值(数值，字符串和布尔值，但这时等同于同步操作)。
(4) 返回值是Promise
async函数的返回值是Promise对象，这比Generator函数的返回值是Iterator对象更加方便，可以使用then方法指定下一步操作。
进一步说，async函数完全可以看作多个异步操作包装成的一个Promise对象，而await命令就是内部then命令的语法糖。
##### 基本用法
async函数返回一个Promise对象，可以使用then方法添加回调函数。当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。
```
    async function demo() {
        return 111;
    }
    demo().then((res) => {
        console.log(res);
    })
```
当async定义的函数有返回值，例如return 111，相当于Promise.resolve(111),没有返回值则相当于Promise.resolve();

当async函数内部抛出错误，会导致返回的Promise对象变为reject状态
##### await命令
await可以理解为是async wait的简写，await必须出现在async函数内部，不能单独使用。

await后面可以跟任何的JS表达式。虽然说await可以跟很多类型的东西，**但是它最主要的意图是用来等待Promise对象的状态被resolved 如果await的是Promise对象，会造成异步函数停止执行并且等待promise的解决。如果等的是正常的表达式则立即执行。**
```
    function timeout(time) {
        return new Promise((resolve,reject) => {
            setTimeout(() => {
                resolve("I'm ok");
            },time);
        })
    }
    function normalFunc() {
        console.log('noramlFunc');
    }
    async function awaitDemo() {
        await normalFunc();
        console.log('aha');
        let result = await timeout(2000);
        console.log(result); // 2s之后打印 "I'm ok"
    }
    awaitDemon();
```

##### 实例
举例说明，假设有三个请求需要发生，第三个请求依赖第二个请求的结果，第二个请求依赖于第一个请求的结果。
首先看用ES5实现
```
    function getResult(url,callback){
        //...
    }
    function RequestAll() {
      getResult(url1, function(res) {
          if(res) {
              getResult(url2,function(res){
                  if(res) {
                      getResult(url3,function(res){
                          if(res){
                              // some code
                          }
                      })
                  }
              })
          }
      })
    }
```

Promise方式实现
```
    function getResult(url,data,callback){
        ...
        return new Promise((resolve,reject) => {
            if(/*success*/) {
                callback(res);
                resolve(res);
            }
        })

    }
    getResult(url1, (res1) => {
        // ...
    }).then((res1) => {
        getResult(url2, res1,(res2) => {
       // 获取到接口1的数据，并且传参调用接口2
        })
    }).then((res2) => {
        getResult(url3, res2, (res3) => {
        // 获取接口2数据，并且传参调用接口3
        })
    }).then((res3) => {
        // 获取到接口3的数据，进行操作
    }).catch((e) => {
        console.log(e);
    })
```

async-await方式实现
```
    function getResult(url,callback){
        ...
        return new Promise((resolve,reject) => {
            if(/*success*/) {
                callback(res);
                resolve(res);
            }
        })

    }
    async function requestAll() {
        let result1 = await getResult(url1,(res) => {
            // ...
        });
        let result2 = await getResult(url2,result1,(res) => {
            // ...
        });
        let result3 = await getResult(url3,result2(res) => {
            // ...
        });
        // do something
    }
    requestAll();
```

##### 错误处理
```
    function timeout(time) {
        return new Promise((resolve,reject) => {
            setTimeout(() => {
                reject('failed...');
            },time)
        })
    }
    async function errorDemo() {
        let result = await timeout(2000);
        console.log(result);
    }
    errorDemo(); // 报错

    // 为了处理Promise.reject的情况，我们要将代码块用try catch包裹一下
    async function errorDemo() {
        try {
            let result = await timeout(2000);
            console.log(result);
        }catch(err) {
            console.log(err);
        }
    }
```

##### 小心并行处理
假设现在有三个异步请求需要发送，并且相互没有关联，只有当三个请求都完成时才进行一些操作。
如果用async await来处理的话例如:
```
    function timeout(time) {
        return new Promise((resolve,reject) => {
            setTimeout(() => {
                resolve('ok');
            },time)
        })
    }
    async function bugDemo() {
        await timeout(1000);
        await timeout(1000);
        await timeout(1000);
        // do to next...
    }
    bugDemo();
```
以上代码实现了在请求全部结束后才执行接下来操作，但是三个请求是串行进行的，由于请求之间并无依赖，所以这种做法是不合理的。
针对这种需求，我们可以采取下列这种方式来实现:
```
    async function correctDemo() {
        let p1 = timeout(1000);
        let p2 = timeout(1000);
        let p3 = timeout(1000);
        await Promise.all([p1,p2,p3]);
        // to do next
    }
    correctDemo();
```

##### await in for 循环
await必须在async函数的上下文中。
```
    // 正常的for循环
    async function forDemo() {
        let arr = [1,2,3,4,5];
        for(let i = 0; i < arr.length; i ++) {
            let result = await arr[i];
            console.log(result); // 正常输出
        }
    }
    forDemo();

    // 使用forEach
    async function forBugDemo() {
        let arr = [1,2,3,4,5];
        arr.forEach(item => {
            let result = item;
            console.log(item); // 报错
        })
    }
    forBugDemo();
```
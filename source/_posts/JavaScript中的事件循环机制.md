---
title: JavaScript中的事件循环机制
date: 2018-07-19 10:30:42
tags: JavaScript
categories: 前端学习笔记(JavaScript)
---



for(var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(new Date,i);
    },1000)
}
console.log(new Date,i);
// 输出结果是: 5 5 5 5 5 5
如果箭头表示1s间隔，逗号表示间隔可以忽略

// 5-> 5,5,5,5,5


如果要求输出 5-> 0,1,2,3,4
for(var i = 0; i < 5; i++) {
    (function(j){
        setTimeout(function(){
            console.log(new Date,j);
        },1000)
    })(i)
}
console.log(new Date,i);

或者

var output = function(i) {
    setTimeout(function() {
        console.log(new Date,i);
    },1000)
}
for(var i = 0; i < 5; i ++) {
    output(i);
}
console.log(new Date,i);


0->1->2->3->4->5
var tasks = [];
const output = function(i) {
    return new Promise(function(resolve,reject) {
        setTimeout(() => {
            console.log(new Date, i);
            resolve();
        },i*1000)
    })
}
for(var i = 0; i < 5; i++) {
    tasks.push(output(i));
}
Promise.all(tasks).then(() => {
    setTimeout(() => {
        console.log(new Date,i)
    },1000)
})

ES7
const sleep = (i,time) => {
    return new Promise((resolve,reject) => {
        setTimeout(() => {
            console.log(new Date,i);
            resolve();
        },time)
    })
}
(async () => {
    for(var i = 0; i < 5; i ++){
        await sleep(i,1000); // 等待Promise状态resolve 等待promise执行完毕再执行
    }
    await sleep(i,1000);
})()



const sleep = (time) => new Promise((resolve) => {
    setTimeout(resolve, time);
})
(async () => {
    for(var i = 0; i < 5; i++) {
        await sleep(1000);
        console.log(new Date,i);
    }
    await sleep(1000);
    console.log(new Date,i);
})()

JavaScript的一大特点就是单线程，而这个线程中拥有唯一的一个事件循环。
JavaScript代码执行的过程中，除了依靠函数调用栈来搞定函数的执行顺序外，还依靠任务队列来搞定另外一些代码的执行。

队列数据结构:
- 一个线程中，事件循环是唯一的，但是任务队列可以拥有多个。
- 任务队列又分为macro-task(宏任务)与micro-task(微任务)
- 宏任务大致包括: script(整体代码)，setTimeout，setInterval，setImmediate，I/O，UI rendering。
- 微任务大致包括: process,nextTick,Promise,Object.observe(已废弃)，MutationObserver(hml5新特性)。
- setTimeout/Promise等我们称之为任务源，而进入任务队列的是他们指定的具体执行任务。
```
    // setTimeout中的回调函数才是进入任务队列的任务
    setTimeout(function() {
        console.log('hello')
    },1000)
```
- 来自不同任务源的任务会进入到 **不同的任务队列**，其中setTimeout与setInterval是同源的。
- 事件循环的顺序，决定了JavaScript代码的执行顺序，它从script(整体代码)开始第一次循环，之后全局上下文进入函数调用栈，直到调用栈清空(只剩全局)，然后执行所有的微任务，当所有的微任务执行完毕之后，循环再从宏任务开始，找到其中一个任务队列执行完毕。然后再执行所有的微任务，这样一直循环下去。
- 其中每一个任务的执行，无论是宏任务还是微任务，都是借助函数调用栈来完成。
```
    setTimeout(function() {
        console.log('timeout1');
    })
    new Promise(function(resolve) {
        console.log('promise1');
        for(var i = 0; i < 1000; i ++){
            i == 99 && resolve();
        }
        console.log(promise2);
    }).then(function() {
        console.log('then1');
    })
    console.log('global1');

    // promise1 promise2 global1 then1 timeout1
```
首先，事件循环从宏任务队列开始，这个时候，宏任务队列中，只有一个script(整体代码)任务。每一个任务的执行顺序，都依靠函数调用栈来搞定，而当遇到任务源时，则会先分发任务到对应的队列中去。
第二步，script任务执行时首先遇到了setTimeout，setTimeout为一个宏任务源，那么它的作用就是将任务分发到它对应的队列中。
第三步，script执行时遇到Promise实例。Promise构造函数中的第一个参数，是在new的时候执行，因此不会进入到任何其他队列，而是直接在当前任务直接执行了，而后续的.then则会被分发到微任务的Promise队列中去。
因此，构造函数执行时，里面的参数进入函数调用栈执行。for循环不会进入任何队列，因此代码会依次执行，所以会依次输出promise1，promise2
resolve在for循环中入栈执行。
script继续往下执行，执行到输出global，然后全局任务执行完毕。
第四步: 第一个宏任务script执行完毕之后，就开始执行所有的可执行的微任务。这个时候，微任务中，只有Promise队列中的一个then1，执行完毕输出then1。当然，他的执行也是进入函数调用栈中执行的。
第五步: 当所有的微任务执行完毕之后，表示第一轮循环结束了。这个时候开始第二轮循环，从宏任务开始，宏任务中只有setTimeout队列中还有一个timeout1的任务，因此此时输出timeout1。


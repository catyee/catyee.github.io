---
title: JavaScript中的事件
date: 2018-07-11 15:59:59
tags: JavaScript
categories: 前端学习笔记(JavaScript)
---
###### 事件冒泡
JavaScript与HTML之间的交互是通过事件实现的。
首先先明确事件流的概念，事件流描述的是从页面中接收事件的顺序。IE的事件流叫做事件冒泡，即事件开始时由最具体的元素(文档中嵌套最深的那个节点)接收，然后逐级向上传播到较为不具体的节点(文档)。如图:![image](/images/event_bubble.png) <center>图片取自JavaScript高级程序设计第三版</center>

###### 事件捕获
Netscape Communicator团队提出的另一种事件流叫做事件捕获。事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件，事件捕获的用意在于在事件到达预定目标之前捕获它。如图: ![image](/images/event_capture.png) <center>图片取自JavaScript高级程序设计第三版</center>


### DOM事件流

“DOM2级事件”规定的事件流包括三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。**首先发生的是事件捕获，为截取事件提供了机会。然后是实际的目标接受事件。最后一个阶段是冒泡阶段**。

### 事件处理程序
响应某个事件的函数就叫做事件处理程序。
- DOM0级事件处理程序
```
    var btn = document.getELementById('#myBtn');
    btn.onclick = function() {
        alert(this.id); // "myBtn"
    }

    btn.onclick = null; //删除事件处理程序
```
- DOM2级事件处理程序
“DOM2级事件”定义了两个方法，用于处理和删除事件处理程序的操作：addEventListener()和removeEventListener（）。所有的DOM节点都包含这两个方法，并且它们都接受3个参数：**要处理的事件名、作为事件处理程序的函数和一个布尔值。最后如果这个布尔值是true，表示在捕获阶段调用事件处理程序；如果是false，表示在冒泡阶段调用事件处理程序。
```
    var btn = document.getELementById('#myBtn');
    btn.addEventListener('click',function() {
        alert(this.id)
    },false)
```

**使用DOM2级方法添加事件处理程序的主要好处是可以添加多个事件处理程序。**
使用addEventListener()添加的事件处理程序只能使用removeEventListener()来移除；移除时传入的参数与添加处理程序时使用的参数相同，这意味着，添加的匿名函数将无法移除

- IE事件处理程序
IE实现了与DOM中类似的两个方法：attachEvent()和detachEvent()这两个方法接受相同的两个参数：事件处理程序名称与事件处理程序函数。这两个事件只支持冒泡
```
    var btn = document.getELementById('#myBtn');
    btn.attachEvent('onclick', function() {
        alert(this.id) // this是window所以这里是undefined
    })
```
**attachEvent()与DOM0级方法的主要区别在于事件处理程序的作用域。DOM0的事件处理程序会在所属元素的作用域内运行，attachEvent()方法的事件处理程序会在全局作用域下运行，因此this等于window**
**attachEvent()也可以用来为一个元素添加多个事件处理程序。但是不是按照事件添加的顺序执行而是以相反的顺序触发这点与addEventListener()相反**
同样必须使用detachEvent()来移除事件，并且传递相同参数
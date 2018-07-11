---
title: JavaScript中的事件
date: 2018-07-11 15:59:59
tags: JavaScript
categories: JavaScript
---
###### 事件冒泡
JavaScript与HTML之间的交互是通过事件实现的。
首先先明确事件流的概念，事件流描述的是从页面中接收事件的顺序。IE的事件流叫做事件冒泡，即事件开始时由最具体的元素(文档中嵌套最深的那个节点)接收，然后逐级向上传播到较为不具体的节点(文档)。如图:![image](/images/event_bubble.png) <center>图片取自JavaScript高级程序设计第三版</center>

###### 事件捕获
Netscape Communicator团队提出的另一种事件流叫做事件捕获。事件捕获的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件，事件捕获的用意在于在事件到达预定目标之前捕获它。如图: ![image](/images/event_capture.png) <center>图片取自JavaScript高级程序设计第三版</center>



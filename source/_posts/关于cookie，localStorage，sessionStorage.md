---
title: 关于cookie，localStorage，sessionStorage
date: 2018-06-25 17:01:33
tags: JavaScript
categories: JavaScript
---
cookie
1.大小限制：
![image](/images/broswer_cookie_size.png)

<center>不同浏览器的cookie限制</center>
所以在进行页面cookie操作的时候，应该尽量保证cookie的个数小于20个，总大小小于4KB。

2.主要用途：

cookie是浏览器提供的功能，cookie其实就是存储在浏览器中的纯文本。

当网页要发http请求时，浏览器会先检查是否有相应的cookie，有则自动添加在request header中的cookie字段中。浏览器会自动为我们完成这些，每一次都会自动帮我们做。既然存储在cookie中的数据，每次都会被浏览器自动放到http请求中，那么如果这些数据并不是每个请求都需要发给服务端，那无疑会增加网络开销。所以队员那些每次请求都要携带的信息，比如身份验证信息，就适合放在cookie中。

3. 创建：

web服务器通过发送一个Set-Cookie的HTTP消息头来创建一个cookie，Set-Cookie消息头是一个字符串，格式如下（中括号中的部分是可选的）：

Set-Cookie：value[;expires=date][;domin=domin][;path=path][;secure]

value部分是一个key=value格式的字符串，通过Set-Cookie设置的可选项（中括号中的内容）只会在浏览器端使用，而不会发送至服务端，发送至服务器的cookie值与通过Set-Cookie指定的值完全一样，不会有进一步的解析和转码操作。

4. 属性选项

cookie的有效时间，expires（http/1.0），max-age（http/1.1），必须是GMT格式的时间（可以通过new Date().toGMTString()或者new Date().toUTCString()来获得）。如果没有设置该选项，在浏览器关闭后cookie就会消失。

expires 是 http/1.0协议中的选项，在新的http/1.1协议中expires已经由 max-age 选项代替，两者的作用都是限制cookie 的有效时间。expires的值是一个时间点（cookie失效时刻= expires），而max-age的值是一个以秒为单位时间段（cookie失效时刻= 创建时刻+ max-age）。

另外，max-age 的默认值是 -1(即有效期为 session )；若max-age有三种可能值：负数、0、正数。负数：有效期session；0：删除cookie；正数：有效期为创建时刻+ max-age

domin是域名，path是路径，二者构成URL，来限制cookie可以被哪些url访问，决定了cookie何时被浏览器自动添加到请求头部发出去，如果没有设置这两个选项，则会使用默认值，domain的默认值为设置该cookie的网页所在的域名，path默认值为设置该cookie的网页所在的目录。

secure选项用来设置cookie只在确保安全的请求中才会发送，当请求是HTTPS协议或者其他安全协议时，包含secure选项的cookie才会被发送至服务器，需要注意的是在http协议的网页中是无法设置secure类型的cookie的

httpOnly，这个选项用来设置cookie能否通过js去访问

localStorage
localstorage是html5提出的，在此之前，IE已经有userData这种实现方式，localStorage是windows的一个对象

1.基本操作

set： localStorage.name = 'test'; 或 localStorage['name']="test"或localStorage.setItem('name','test')；

get:  var name = localStorage.name; 或 var name = localStorage['name'];或 var name = localStorage.getItem('name');

localStorage还提供了key方法，来获取里面所有的key

如： var storage = window.localStorage;

        for(var i = 0; i < storage.length;i++) {

                    var key = strorage.key(i);

                    var value = storage.getItem(storage.key(i))

            }

delete：localStorage.removeItem('name');

clearAll: localStorage.clear();

相关事件：

html5的本地存储，还提供了一个storage事件，可以对键值对的改变进行监听：

    if(window.addEventListener){

        window.addEventListener('storage',handle_change,false);

    }else if (window.attachEvent) {

        window.attachEvent('onStorage',handle_change)

    }

function handle_change(e) {

    if (!e) e = window.event;

}



localStorage是持久化的本地存储，除非主动删除数据否则数据永远不会过期，受同源策略的限制

应用场景：

可以用来存储浏览器对该页面的访问次数，当前，如果换个浏览器这个次数就要重新开始计数了，还可以用来存储一些固定不变的页面信息，这样就不需要每次都重新加载了，这个值也可以进行覆盖

sessionStorage
方法同localStorage

用于本地存储一个会话中的数据，这些数据只有在同一个会话的页面才能访问并且当前会话结束后数据也随之销毁，因此sessionStorage不是一种持久化的本地存储，仅仅是一种会话级别的存储，只要这个浏览器窗口没有关闭，即使刷新页面或者进入同源另一页面，数据仍然存在，关闭窗口后，sessionStorage即被销毁

应用场景： 页面之间的传值，从A页面跳转到B页面sessionStorage不会销毁


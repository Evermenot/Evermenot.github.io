---
title: 简单记录Web storage、cookie、session的区别
date: 2019-03-20 16:09:37
tags: Javascript
categories: [技术, JavaScript]
---

###  Web storage 和 cookie

- Web storage（HTML5新增客户端存储数据方法）：
  - localStorage - 没有时间限制的数据存储；
  - sessionStorage - 会话级数据存储（关闭页面即消失）；
- cookie
  - 如果cookie没有设置过期时间，则称为会话级cookie（关闭页面即失效），会话cookie时保存在内存上的，而设置了过期时间的cookie是存储在硬盘上的



- 共同点：
  - 都保存在浏览器端，且是同源的；
- 区别：
  - cookie数据始终在同源的http请求中携带，而Web storage是本地存储，不与服务器进行交互通信；
  - 存储大小区别，cookie是4k，Web storage可以达到5M甚至更大；
  - 有效期：localStorage没有时间限制，sessionStorage 只在浏览器关闭前有效，cookie 设置的过期时间前有效；
  - 作用域区别：sessionStorage只在同一个浏览器窗口中共享，localStorage 和 cookie 在所有同源窗口是共享的（如：两个chrome窗口）
- 作用区别：
  - Web storage支持事件通知机制，可以将数据更新的通知发送给监听者。Web storage的 api 接口使用更方便。
  - Web storage拥有setItem,getItem,removeItem,clear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。
  - cookie也是不可以或缺的：cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web storage仅仅是为了在本地“存储”数据而生。
  - cookie:服务器和客户端都可以访问；大小只有4KB左右；有有效期，过期后将会删除；
  - 本地存储：只有本地浏览器端可访问数据，服务器不能访问本地存储直到故意通过POST或者GET的通道发送到服务器；每个域5MB；没有过期数据，它将保留知道用户从浏览器清除或者使用Javascript代码移除

### cookie 和 session

- **session机制**
  - session存放在服务端，每一个session有一个sessionID。
  - 客户端发起请求的时候会带上sessionID
  - 如果没有sessionID，在服务端会新建一个sessionID，然后返回给客户端



- 共同点：

  - 都是用来跟踪浏览器用户身份的会话方式。

- 区别：

  - 保持状态：cookie保存在浏览器端，session保存在服务器端

  - session机制的使用依赖于cookie

  - 存储内容：cookie只能保存字符串类型，以文本的方式；session能支持任何类型的对象(session中可含有多个对象)

  - 存储的大小：cookie：单个cookie保存的数据不能超过4kb；session大小没有限制。

    注：感觉这也是session作为一个单独的存储机制，存在的原因，不然直接在cookie存储不就行了？

[相关文章](https://www.cnblogs.com/cencenyue/p/7604651.html)
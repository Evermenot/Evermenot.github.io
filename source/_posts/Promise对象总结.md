---
title: Promise对象总结
date: 2019-03-15 11:41:05
tags: JavaScript
categories: [技术, JavaScript]
---

### 前言

艹艹艹都差不多写完了，莫名其妙的变成空白文档，没了......what fuck（黑人问号ing）

靠我干什么了？什么也没干啊我，泪奔。。。重写吧。

目的：知识总结，加深印象

注：本文中的知识点多是来自阮老师的ES6标准入门一书

### 定义

> Promise 是异步编程的一种解决方案，比传统的解决方案，更合理和更强大。将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数
>
> 总的来说就是一种新的编写（异步）代码的方式

#### 缺点：

> 首先，无法取消Promise，一旦新建它就会立即执行，无法中途取消。
>
> 其次，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
>
> 第三，当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

### 用法

#### 创建Promise实例

~~~javascript
const promise = new Promise(function(resolve, reject) {
  if (/* 异步操作成功 */){
    return resolve('value'); // 将成功的结果传递出去
  } else {
    return reject(error);
  }
});

注：resolve函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
~~~

#### then()方法

~~~javascript
promise.then(function(value) {
  // success
  console.log(value)	// 字符串'value'
}, function(error) {
  // failure
});

注1：then方法的第一个参数是resolved状态的回调函数，第二个参数（可选）是rejected状态的回调函数。这两个函数都接受Promise对象传出的值作为参数。

注2：then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即then方法后面再调用另一个then方法。
~~~

#### catch()方法

> catch方法是.then(null, rejection)或.then(undefined, rejection)的别名，用于指定发生错误时的回调函数。

~~~javascript
promise.then(function(value) {
  // ...
}).catch(function(error) {
  console.log('发生错误！', error);
});

注：一般来说，不要在then方法里面定义 Reject 状态的回调函数（即then的第二个参数）

// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
注：第二种写法要好于第一种写法，理由是第二种写法可以捕获前面then方法执行中的错误，
~~~

#### finally()方法

> `finally`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

~~~javascript
// 例子，服务器使用 Promise 处理请求，然后使用finally方法关掉服务器
server.listen(port)
  .then(function () {
    // ...
  })
  .finally(server.stop);

finally方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是fulfilled还是rejected。这表明，finally方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。
~~~

#### all()方法

> all方法用于将多个 Promise 实例，包装成一个新的 Promise 实例

~~~javascript
const p = Promise.all([p1, p2, p3])
  .then([data1, data2, data3] => {
  	// 已拿到全部的data，可以处理了
  });
~~~

#### 例子

```javascript
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});
// 2
// 1
调用resolve(1)以后，后面的console.log(2)还是会执行，并且会首先打印出来。这是因为立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。

new Promise((resolve, reject) => {
  return resolve(1);
  // 后面的语句不会执行
  console.log(2);
})
一般来说，调用resolve或reject以后，Promise 的使命就完成了，后继操作应该放到then方法里面，而不应该直接写在resolve或reject的后面。所以，最好在它们前面加上return语句，这样就不会有意外。
```

~~~javascript
Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve方法

const p = Promise.resolve();
p.then(function () {
  // ...
});
~~~


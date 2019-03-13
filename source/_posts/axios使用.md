---
title: axios使用
date: 2019-03-07 18:53:50
tags: axios
categories: [技术, axios]
---

### 前言

一直听说用Axios来发送http请求有很多优点，之前只是随大流简单的使用了下，最近因为项目调整前端这块需求减少，那么闲下来总得找点事干（毕竟旁边坐着老大呢，还有老大的老大~），解惑是个很不错的选择，所以就查找资料写了这篇Axios的调研博客，目的是为了降低下次使用的时间成本。

**相关参考文章**

[看云](https://www.kancloud.cn/yunye/axios/234845)

[vue 2之 axios](https://segmentfault.com/a/1190000013071458)

[Jquery ajax, Axios, Fetch区别之我见](https://segmentfault.com/a/1190000012836882)

### 定义

- Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。
- 本质上也是对原生XHR的封装，但它是Promise的实现版本，符合最新的ES规范

### 特性

- 支持在node.js中创建 http 请求
- 支持 Promise API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防止CSRF
- 提供了一些并发请求的接口（重要，方便了很多的操作）

```
支持防止CSRF，就是让每个请求都带一个从cookie中拿到的key, 根据浏览器同源策略，假冒的网站是拿不到cookie中得key的，这样后台就可辨别请求是否是用户在假冒网站上的误导输入，从而采取正确的策略。

Axios既提供了并发的封装，也没有fetch的各种问题，并且体积也较小
```

### 具体功能

#### get请求

```javascript
import axios from 'axios'
//【】代表可选
axios.get(url【, params】)
.then(rsp => {console.log(rsp)})
.catch(error => {console.log(error)})

// 配置写法：
axios({
  method:'get',
  url:'http://lecode.ltd',
  data
})
.then(rsp => {console.log(rsp)})
.catch(error => {console.log(error)})
```

#### post请求

```javascript
axios.post(url【, qs.stringify(params)】)
.then(rsp => {console.log(rsp)})
.catch(error => {console.log(error)})

// post参数格式：
	1. form-data: ?name=iwen&age=10
	2. x-www-form-urlencoded: {name:'iwen',age:20}
// 注： 插件“qs”，可以将请求参数转换为form-data格式。
```

#### 并发请求

```javascript
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成
  }));

// 并发请求函数
axios.all(iterable)
axios.spread(callback)
```

#### 请求拦截

```javascript
// 添加请求拦截器
Axios.interceptors.request.use(config => {
    // 在发送请求之前做些什么，如：
    
    if(config.method === 'post'){
  	//将请求参数进行转换，这里是全局配置post请求参数
    	config.data = qs.stringify(config.data)
  	}
    
    return config;
  }, error => {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
Axios.interceptors.response.use(
    response => response
   , error => Promise.reject(error)
);
```

#### 创建实例

```javascript
// 利用实例可以设置些公共配置
const instance = axios.create({
    baseURL: '/api/',
  	timeout: '1000', // 请求超时时间（毫秒）
  	headers: {'X-Requested-With': 'XMLHttpRequest'},
})
// 上面的配置实际上还有很多
instance.get('/person', { params })
```

### 使用

~~~javascript
import axios from 'axios'
import jsonpAdapter from 'axios-jsonp'
import Qs from 'qs'

const signinUrl = 'http://passport.oa.com/modules/passport/signin.ashx'

// Vue.prototype.axios = axios
// axios.defaults.withCredentials=true
axios.interceptors.response.use(function (response) {
  return response;
}, function (error) {
  // debugger;
  console.log(error)
  if(!error.response) {  //无response，默认已经302到passport.oa，刷新页面
    console.log('未登录，请先登录');
    // window.location.reload();

    location.href = `${signinUrl}?url=${encodeURIComponent(location.href)}`;
  }
  // Do something with response error
  return Promise.reject(error);
});


const handleStatus = (res) => {
  return res.data
}
const handleResponse = (res) => {
  if (!!res) {
    return Promise.resolve(res)
  } else {
    return Promise.reject(res)
  }
}
export default {
  get (url, params) {

    let _obj = {
        url: url,
        params: params
    };

    //本地请求。。用jsonp吧
    if(location.hostname == 'localhost'){
        _obj.adapter = jsonpAdapter;
    };

    return axios(_obj)
      .then(handleStatus)
      .then(handleResponse)
      .catch(error => {
        console.log(error)
      })
  },

  jsonp (url, params) {

    return axios({
        url: url,
        adapter: jsonpAdapter,
        params: params
      })
      .then(handleStatus)
      .then(handleResponse)
      .catch(error => {
        console.log(error)
      })
  },

  post (url, params) {
    let data =  Qs.stringify(params);
    return axios({
        method: 'POST',
        headers: {
            'content-type': 'application/x-www-form-urlencoded'
        },
        data,
        url,
      })
      .then(handleStatus)
      .then(handleResponse)
      .catch(error => {
        console.log(error)
      })
  }
}
~~~

### Jquery ajax 和 Fetch优缺点

- Jquery ajax

  ```
  介绍：
      是对原生XHR的封装
  优点：
      使用方便，支持JSONP请求
  缺点：
      本身是针对MVC的编程,不符合现在前端MVVM的浪潮
      基于原生的XHR开发，XHR本身的架构不清晰，已经有了fetch的替代方案
      只使用ajax而引入整个JQuery太大（采取个性化打包的方案又不能享受CDN服务）
  ```

- Fetch

  ```javascript
  介绍（结合下面代码了解）：
      符合关注分离，没有将输入、输出和用事件来跟踪的状态混杂在一个对象里;
      更好更方便的写法;
  	try {
    		let response = await fetch(url);
    		let data = response.json();
    		console.log(data);
  	} catch(e) {
    		console.log("Oops, error", e);
  	}
  优点：
      更加底层，提供的API丰富（request, response）
      脱离了XHR，是ES规范里新的实现方式
  缺点：
      低层次的API，可考虑成原生的XHR，使用起来不舒服，需要进行封装

  1）fetch只对网络请求报错，对400，500都当做成功的请求，需要封装去处理
  2）fetch默认不会带cookie，需要添加配置项
  3）fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
  4）fetch没有办法原生监测请求的进度，而XHR可以

  注：fetch在前端的应用上有一项xhr怎么也比不上的能力：跨域的处理。
  ```


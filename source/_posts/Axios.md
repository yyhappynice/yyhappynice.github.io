---
title: Axios
date: 2019-03-20 17:25:11
tags:
- js
categories: Axios
thumbnail: https://user-images.githubusercontent.com/12566627/56471199-5cfb2600-6482-11e9-988c-b5ec24476492.png
---
我们常常使用 axios 做接口请求库，它并且也是 vue 官方推荐的。axios 是一个基于 promise 的 http 库，同时支持浏览器与node端，下面我们就了解一下 axios 吧～ 🤗  ️

Axios 的主要特性包括：
>从浏览器发出XMLHttpRequests
从node.js发出HTTP请求
支持Promise API
拦截请求和响应
转换请求和响应数据
取消请求
JSON数据的自动转换
客户端支持以防止XSRF

## axios源码阅读
接下来我们结合源码，来具体看一下 axios 内部是如何实现的（不过这里小小吐槽一下 axios 还是在用 grunt 打包的～😳哈哈 ）

#### 目录结构
首先看一下 axios 项目的目录结构：

```js
├── /dist/                     # 源码打包输出目录
├── /lib/                      # 项目源码目录
│ ├── /adapters/               # 定义请求的适配器 xhr、http
│ │ ├── http.js                # node端请求封装
│ │ └── xhr.js                 # 客户端 XMLHttpRequest 的封装
│ ├── /cancel/                 # 取消requests功能
│ ├── /core/                   # 一些核心功能
│ │ ├── Axios.js               # axios实例构造函数
│ │ ├── InterceptorManager.js  # 拦截器构造函数
│ │ ├── createError.js         # 抛出错误
│ │ ├── dispatchRequest.js     # 用来调用http请求适配器方法发送请求
│ │ ├── mergeConfig.js         # 合并参数
│ │ ├── settle.js              # 根据http响应状态，改变Promise的状态
│ │ └── transformData.js       # 改变数据格式
│ ├── /helpers/                # 一些辅助方法
│ ├── axios.js                 # 对外暴露接口
│ ├── defaults.js              # 默认配置
│ └── utils.js                 # 公用方法文件 判断各种类型和常用方法的封装
├── index.d.ts                 # 配置TypeScript的声明文件
├── package.json               # 项目信息
└── index.js                   # 入口文件
```

#### API 使用
|  API  | 类型 |
|  ----  | ----  |
| axios(config) | 将相关配置传递给 axios 来发出请求 |
| axios.create(config) | 创建 Axios 实例设置默认参数，继承原型方法 |
| axios.request(get, post, put, options ...) | 请求方法别名, 支持各种http请求方法 |
| axios.all(iterable) / axios.spread(callback) | 处理并发请求 |
| axios.Cancel / axios.CancelToken / axios.isCancel | 请求取消 |
| transformRequest / transformResponse | 转换请求与响应数据 |
| axios.interceptors | 定义请求或响应的拦截器 |

#### 工具方法使用
axios 中定义了一些常用的方法，并在项目中多处使用，简单可以看一下
1、utils.extend
将一个对象的方法和属性扩展到另外一个对象上，并指定上下文，例如：utils.extend(target, source, context)，会将 source 对象下的方法和属性扩展到 target 上，并把 this 指向到 context 上面。

2、utils.forEach
既可以遍历对象也可以遍历数组，对原生的 forEach 方法做了扩展。
>遍历数组 utils.forEach([], (value, index, array) => {})
遍历对象 utils.forEach({}, (value, key, object) => {})

3、bind
给某个函数指定上下文，也就是this指向。bind(fn, context); 实现效果同Function.prototype.bind方法: fn.bind(context)

4、merge
深度合并多个对象为一个对象


------
#### 源码分析
首先从入口文件入手，在 index.js 中引入了 `'./lib/axios'`，在 [axios.js](https://github.com/axios/axios/blob/master/lib/axios.js) 文件中创建了 axios 实例继承 Axios 类，同时在实例下定义一些常用方法，例如： create、all、cancel、spread，最后将实例 axios 导出暴漏给外部使用，如下我看一下具体源码实现。

```js
// lib/axios.js
var utils = require('./utils');
var bind = require('./helpers/bind');
var Axios = require('./core/Axios');
var mergeConfig = require('./core/mergeConfig');
var defaults = require('./defaults');

// 通过 createInstance 的方法，返回Axios实例
function createInstance(defaultConfig) {
    // new Axios 得到一个 Axios 实例，将默认配置传入构造函数中
    var context = new Axios(defaultConfig);
    // instance 实例为bind返回的一个函数(即是request发送请求方法)，此时 this 绑定到 context 上下文环境
    var instance = bind(Axios.prototype.request, context);

    // 将Axios.prototype复制到实例
    utils.extend(instance, Axios.prototype, context);

    // 将上下文复制到新的axios实例上
    utils.extend(instance, context);

    return instance;
}

// 创建默认的实例并导出 axios
var axios = createInstance(defaults);

// 暴漏出 Axios 类以允许类继承
axios.Axios = Axios;

// 创建工厂方法, 对 createInstance 进行封装，返回一个新的 Axios 实例，接收配置 config 对象与默认配置进行合并。
axios.create = function create(instanceConfig) {
    return createInstance(mergeConfig(axios.defaults, instanceConfig));
};


// 绑定取消请求的相关方法
axios.Cancel = require('./cancel/Cancel');
axios.CancelToken = require('./cancel/CancelToken');
axios.isCancel = require('./cancel/isCancel');

// all 方法，其实就是对 Promise.all 的封装，接收一个 promises 数组
axios.all = function all(promises) {
  return Promise.all(promises);
};
axios.spread = require('./helpers/spread');

module.exports = axios;

// 允许在TypeScript中使用默认的导入语法
module.exports.default = axios;
```
通过以上代码，我们可以知道 axios 入口其实就是通过 createInstance 创建出来的实例，与 axios.create() 创建出的实例相同，createInstance 流程大致为:
1、创建一个 Axios 的实例 context, 并将默认参数 defaultConfig 传入
2、根据 Axios.prototype.request 和 上下文 创建实例 instance，实例为一个 request 发送请求的函数 this 指向上下文 context
3、将原型上 Axios.prototype 的其他方法，绑定到 instance 实例，并将 this 指到上下文 context 上
4、把 context 中的默认配置 defaults 和拦截器 interceptors 绑定到 instance 实例上

__Axios 构造函数__
Axios 构造函数其实就是 axios 整个包的核心，其他方法都是对Axios内容的扩展，在[lib/core/Axios.js](https://github.com/axios/axios/blob/master/lib/core/Axios.js)文件中定义了 Axios 构造函数，同时也定义了 interceptors 拦截器、request 原型方法和各种请求的别名。

```js
// lib/core/Axios.js
var utils = require('./../utils');
var buildURL = require('../helpers/buildURL');
var InterceptorManager = require('./InterceptorManager');
var dispatchRequest = require('./dispatchRequest');
var mergeConfig = require('./mergeConfig');

function Axios(instanceConfig) {
    this.defaults = instanceConfig;
    this.interceptors = {
        request: new InterceptorManager(),
        response: new InterceptorManager()
    };
}

Axios.prototype.request = function request(config) {
    // ...
}

// Provide aliases for supported request methods
utils.forEach(['delete', 'get', 'head', 'options'], function forEachMethodNoData(method) {
    Axios.prototype[method] = function(url, config) {
        return this.request(utils.merge(config || {}, {
            method: method,
            url: url
        }));
    };
});

utils.forEach(['post', 'put', 'patch'], function forEachMethodWithData(method) {
    Axios.prototype[method] = function(url, data, config) {
        return this.request(utils.merge(config || {}, {
            method: method,
            url: url,
            data: data
        }));
    };
});
```
我们注意到这里调用了 forEach 方法，主要是将请求别名挂载到 Axios 原型上，这样我们通过 axios.get()、axios.post() 的方式来调用，其实最终都是掉用的 this.request 方法，两次 forEach 是因为 post 、 put 、 patch 含有请求体，所以分开处理。

__拦截器__
在 Axios 构造函数中，我们注意到有一个 interceptors 属性，这个属性下含有 request 和 response 两对象都分别继承了 InterceptorManager 构造函数，这个构造函数主要，定义了操作拦截器数组添加、移除的方法
```js
function InterceptorManager() {
    this.handlers = [];
};

// 添加新的拦截器到堆栈中，返回当前项的索引值
InterceptorManager.prototype.use = function use(fulfilled, rejected) {
    this.handlers.push({
        fulfilled: fulfilled,
        rejected: rejected
    });
    return this.handlers.length - 1;
};

// 从堆栈中删除拦截器
InterceptorManager.prototype.eject = function eject(id) {
    if (this.handlers[id]) {
        this.handlers[id] = null;
    }
};

// 遍历this.handlers，并将this.handlers里的每一项作为参数传给fn执行
InterceptorManager.prototype.forEach = function forEach(fn) {
    utils.forEach(this.handlers, function forEachHandler(h) {
        if (h !== null) {
            fn(h);
        }
    });
};
```
InterceptorManager 构造函数来实现拦截器，这个构造函数原型上有3个方法：use、eject、forEach，操作该构造函数中的 handlers 实例属性，当我们成功添加拦截器之后，我们需要在请求的前后对数据进行处理，这也就是在 Axios.protoType.request 中做了相关的处理。

__Axios.protoType.request__
在 Axios 原型上定义了最核心的方法 request，axios 中请求最终是由 request 方法发出的。
```js
/**
 * Dispatch a request
*/
Axios.prototype.request = function request(config) {
    var chain = [dispatchRequest, undefined];
    return promise;
};
```





支持客户端XSRF攻击防护
XSRF攻击，即“跨站请求伪造”(Cross Site Request Forgery)攻击。通过窃取用户cookie，让用户在本机（即拥有身份 cookie 的浏览器端）发起用户所不知道的请求。防护XSRF攻击的一种方法是设置特殊的xsrf token，axios实现了对这种方法的支持：
在默认配置文件中设置
```js
{
  // `xsrfCookieName` is the name of the cookie to use as a value for xsrf token
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` is the name of the http header that carries the xsrf token value
  xsrfHeaderName: 'X-XSRF-TOKEN', // default
}
```


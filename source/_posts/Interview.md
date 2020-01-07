---
title: Interview
date: 2018-12-14 09:42:18
tags:
- js
categories: 前端面试题汇总
thumbnail: https://user-images.githubusercontent.com/12566627/66701668-464ad300-ed31-11e9-94cd-14cba6b713a7.jpg
---
前端面试题汇总，不定期更新～ 😋

## 前端面试题汇总

### js 相关
__`['1', '2', '3'].map(parseInt)`__

2、call实现
```js
Function.prototype.call = function (context) {
  context = context ? Object(context) : window;
  var fn = Symbol(); // added
  context[fn] = this; // changed

  let args = [...arguments].slice(1);
  let result = context[fn](...args); // changed

  delete context[fn]; // changed
  return result;
}
```
__this 指向__
>this的指向是在函数调用的时候确定下来的, this的指向大致可以分为五种。
1、 默认绑定: 指向全局对象，严格模式为undefined
2、 隐式绑定: 谁调用 this 就指向谁
3、 显示绑定call,apply,bind
4、 new绑定
5、 es6的箭头函数 (箭头函数的 this 始终指向函数定义时的 this，而非执行时)
this绑定优先级：
箭头函数 -> new绑定 -> 显示绑定call/bind/apply -> 隐式绑定 -> 默认绑定

__数组扁平化__
```js
//已知如下数组：
//var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];
//编写一个程序将数组扁平化去并除其中重复部分数据，最终得到一个升序且不重复的数组。[题目参考](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/8)
[...new Set(arr.toString().split(",").sort((a,b)=>{ return a-b }))]
//或
Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{ return a-b})
```
```js
function flatten(arr) {
    return arr.reduce((result, item)=> {
        return result.concat(Array.isArray(item) ? flatten(item) : item)
    }, [])
}
```
__React 中 setState 什么时候是同步的，什么时候是异步的？__
[解答](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/17)

__实现一个 sleep 函数__
```js
const sleep = (time) => {
  return new Promise(resolve => setTimeout(resolve, time))
}
```

__为什么在 Vue3.0 采用了 Proxy，抛弃了 Object.defineProperty__
(1) Object.defineProperty无法监控到数组下标的变化，导致通过数组下标添加元素，不能实时响应；
(2) Object.defineProperty只能劫持对象的属性，从而需要对每个对象，每个属性进行遍历，如果，属性值是对象，还需要深度遍历。Proxy可以劫持整个对象，并返回一个新的对象。
(3) Proxy不仅可以代理对象，还可以代理数组。还可以代理动态增加的属性。
[解答](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/90)


__instanceof 的实现__
```js
function instanceof(L, R) { //L是表达式左边，R是表达式右边
    var O = R.prototype;
    L = L.__proto__;
    while(true) {
        if (L === null)
            return false;
        if (L === O)
            return true;
        L = L.__proto__;
    }
}
```

__如何将一个数组打乱__
```js
arr.sort(() => Math.random() - 0.5)
//sort方法使用了插入排序（目标长度小于10）和快排，元素之间的比较远小于n(n-1)/2，因此有些元素间没有随机交换的可能，使得该方法不够随机。
function shuffle(arr) {
    let m = arr.length;
    while (m > 1){
        let index = Math.floor(Math.random() * m--);
        [arr[m] , arr[index]] = [arr[index] , arr[m]]
    }
    return arr;
}
//复杂度为O(n)。从后向前遍历，不断将当前元素与随机位置的元素（除去已遍历的部分）进行交换。
```

__trim 方法模拟__
```js
function trim(str) {
    return str.replace(/^\s+|\s+$/g, '')
}
```
__千分位分隔__
```js
function format(str) {
    return str.replace(/(\d)(?=(?:\d{3})+$)/g, '$1,')
}
```


__Vue 组件 data 为什么必须是函数__
>当一个组件被定义， data 必须声明为返回一个初始数据对象的函数，因为组件可能被用来创建多个实例。如果 data 仍然是一个纯粹的对象，则所有的实例将共享引用同一个数据对象！通过提供 data 函数，每次创建一个新实例后，我们能够调用 data 函数，从而返回初始数据的一个全新副本数据对象。

__JS数据类型判断__
Javascript 7 种数据类型包含 基本类型：String、Number、Boolean、Symbol、Undefined、Null，引用类型：Object

>1、typeof : typeof可以测试出number、string、boolean、undefined、function 及 Object (null及数组都为Object)
2、instanceof : instanceof 来判断否为实例，instanceof 判断两个对象是否属于实例关系， 但不能判断对象实例具体属于哪种类型。
3、constructor : 当一个函数F被定义时，JS引擎为函数添加 prototype 原型，再在 prototype 上添加一个 constructor 属性，并让其指向 F 的引用, var f = new F() 实例的 constructor 指向F f.constructor == F (例：''.constructor === String)
4、toString ：Object 的原型方法调用 toString (例 Object.prototype.toString.call(''))

[解答](https://www.cnblogs.com/onepixel/p/5126046.html)

__网页从输入网址到渲染完成经历了哪些过程__

<details>
<summary>CLICK ME</summary>
1、浏览器输入网址
2、域名解析：浏览器缓存 ➡️ 系统缓存 ➡️ 本地host文件 ➡️ 路由器缓存 ➡️ 智能DNS查询
3、三次握手以建立TCP连接
4、浏览器请求资源文件html、js、css，http的缓存(强缓存或、协商缓存)
5、浏览器拿到html构建DOM树，获得外部CSS文件的数据构建CSSOM树，再将DOM树与CSSOM树结合在一起生成render tree，后遍历渲染树开始布局（执行顺序JavaScript执行、style样式计算、layout布局、paint绘制、composite合成）
6、当浏览器的HTML解析器遇到一个script标记时会暂停构建DOM，加载js文件，解析器进行词法分析成词法单元、语法分析生成AST(最后转换成机器码)、预编译(先变量提升，在预定义函数)、解释执行js
7、js如果样式或DOM有更改，则浏览器会重新生成render tree，进行重绘和重排
</details>

__Promise 实现__
[1、](https://zhuanlan.zhihu.com/p/60836800)
[2、](https://juejin.im/post/5df4fac1518825125c430544)

__如何停掉 Promise 链__
[链接](https://github.com/xieranmaya/blog/issues/5)

__Promise.all 使用及实现__
[链接](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/130#issuecomment-535074526)
[链接](https://segmentfault.com/a/1190000014424410)
__实现 Promise.finally__
```js
Promise.prototype.finally = function (fn) {
  return this.then(value => {
        fn()
        return value
    }, reason => {
        fn()
        throw reason
    });
}
```
__resolve/reject__
```js
static resolve(val) {
    return new Promise((resolve, reject) => {
        resolve(val)
    })
}
static reject(val) {
    return new Promise((resolve, reject) => {
        reject(val)
    })
}
```
__Promise实现 timeout__
[链接](https://www.cnblogs.com/shytong/p/5681568.html)

__理解 ES6 Iterator__
[链接](https://juejin.im/post/5dd3540df265da0c0a143f5d)

__Javascript中装饰器的实现原理__
[Javascript中装饰器的实现原理](https://github.com/zz1211/Doraemon/issues/1)
[Javascript 中的装饰器](https://aotu.io/notes/2016/10/24/decorator/index.html)

__setState 同步异步__
[深入setState](https://luckyabby.com/posts/%E6%B7%B1%E5%85%A5setstate/)

__BFC 概念__
[理解 BFC 原理](https://zhuanlan.zhihu.com/p/25321647)

__webpack Scope Hoisting__
[Scope Hoisting](https://imweb.io/topic/5a43064fa192c3b460fce360)
### http 相关
[http状态码](https://tool.lu/httpcode/)
[总结](https://github.com/forthealllight/blog/issues/19)

### node 相关
(10 道 Nodejs 进程相关面试题)[https://mp.weixin.qq.com/s?__biz=MzIyNDU2NTc5Mw==&mid=2247483763&idx=3&sn=bc42cd19a05044280e3b1061adbc8225&chksm=e80c4e3ddf7bc72bc1891f624b6e74ada7ca868337f5f002cf14368f372b08c66e397828ac5e&token=108126567&lang=zh_CN#rd]

### 二叉树
[链接](https://segmentfault.com/a/1190000016914803)
## 参考
[Daily-Interview-Question](https://github.com/Advanced-Frontend/Daily-Interview-Question)
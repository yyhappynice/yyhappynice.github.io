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
1、`['1', '2', '3'].map(parseInt)`

2、bind实现

3、this 指向

4、数组扁平化

```js
//已知如下数组：
//var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];
//编写一个程序将数组扁平化去并除其中重复部分数据，最终得到一个升序且不重复的数组。[题目参考](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/8)
arr.toString().split(",").sort((a,b)=>{ return a-b })
```

5、React 中 setState 什么时候是同步的，什么时候是异步的？
[解答](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/17)

6、实现一个 sleep 函数
```js
const sleep = (time) => {
  return new Promise(resolve => setTimeout(resolve, time))
}
```
7、为什么在 Vue3.0 采用了 Proxy，抛弃了 Object.defineProperty
(1) Object.defineProperty无法监控到数组下标的变化，导致通过数组下标添加元素，不能实时响应；
(2) Object.defineProperty只能劫持对象的属性，从而需要对每个对象，每个属性进行遍历，如果，属性值是对象，还需要深度遍历。Proxy可以劫持整个对象，并返回一个新的对象。
(3) Proxy不仅可以代理对象，还可以代理数组。还可以代理动态增加的属性。
[解答](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/90)


8、instanceof 的实现
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
### http 相关



## 参考
[Daily-Interview-Question](https://github.com/Advanced-Frontend/Daily-Interview-Question)
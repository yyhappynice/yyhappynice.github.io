---
title: # new
date: 2018/12/03 13:48:25
tags:
- js
categories: new
thumbnail: https://atom-morgan.github.io/images/hello-world/tutorial-hello-world.png
---
今天我们来介绍一下new 😋 用一句话来概括：new 运算符创建一个用户定义的__对象类型的__实例或具有构造函数的__内置对象__的实例。

## new运算符


### 使用
我们平时使用一般分为两步
1、通过编写函数来定义函数对象
2、通过new来创建对象实例

举个例子：🌰

```js
function P(name, age) {
    this.name = name
    this.age = age
}

P.prototype.sayYourName = function () {
    console.log('I am ' + this.name)
}

var person = new P('Tom', 20)

console.log(person.name) // Tom
console.log(person.age) // 20

person.sayYourName() // I am Tom
```
可以看到 实例 person 可以：
1、访问到 P 构造函数里的属性
2、访问到 P.prototype 原型中的属性

### 实现

当代码执行 `new Foo` 时，会发生以下过程

> 1、创建一个新对象继承自 Foo.prototype
> 2、将构造函数中的 this 绑定到新对象上，并调用构造函数 Foo

具体实现如下 ：

```js
function objectFactory() { // 我们使用函数 来模拟new关键字

    var obj = new Object(), // 创建新的对象

    Constructor = [].shift.call(arguments) // 取出第一个参数 构造函数

    obj.__proto__ = Constructor.prototype // 使obj.__proto__ 指向构造函数的原型

    Constructor.apply(obj, arguments) // 调用构造函数，将 this 指向 obj

    return obj
}
```

注：每个对象都有`__proto__`属性，函数对象下才有constructor属性

### 扩展
我们还可以通过`Object.create`的方式创建对象
先看一下具体实现

```js
Object.create =  function (o) {
    var F = function () {}
    F.prototype = o
    return new F()
}
```
我们在使用时一般传入__构造函数的原型__ Foo.prototype 来实现，继承原型属性和方法～

看个例子：🌰
```js
var Base = function () {
    this.a = 2
}

var o1 = new Base()
var o2 = Object.create(Base)

console.log(o1.a) // 2
console.log(o2.a) // undefined
```
所以可以看出 `Object.create` 失去了对原有对象属性的访问，但使用 `new` 依然可以访问

##### Object.create(null) 与 new Object()

我们在使用 `Object.create(null)` 与 `new Object()` 时，两者其实都是创建空对象，但 `new` 创建出的对象会绑定 `Object.prototype` 原型对象，而 `Object.create(null)` 创建的则没有任何属性的纯空对象。

### 参考

[MDN NEW OPERATOR](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)
[JavaScript深入之new的模拟实现](https://github.com/mqyqingfeng/Blog/issues/13)

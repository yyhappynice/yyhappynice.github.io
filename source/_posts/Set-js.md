---
title: set
date: 2019/01/26 12:48:60
tags:
- js
categories: js-set
thumbnail: https://user-images.githubusercontent.com/12566627/56256114-f086cc80-60f9-11e9-8602-a1d07a16fbdc.jpeg
---
手把手教你实现js中的Set类。😜

### 简介
`Set`对象是值的集合，你可以按照插入的顺序迭代它的元素。 `Set`中的元素只会出现一次，即 Set 中的元素是唯一的。
> 值的相等
1、`Set` 中的值总是唯一的, 所以需要判断两个值是否**全等**。
2、`NaN` 和 `undefined` 都可以被存储在Set 中，`NaN`之间被视为相同的值(尽管 `NaN !== NaN`)

### 使用

##### `Set`实例

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;所有`Set`实例继承自 `Set.prototype`

###### 属性

`Set.prototype.constructor`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回实例的构造函数。默认情况下是`Set`。

`Set.prototype.size`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回`Set`对象的值的个数。

------

<!-- 操作方法 -->

###### 操作方法

`Set.prototype.has(value)`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回一个布尔值，表示该值在Set中存在与否。

`Set.prototype.add(value)`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在`Set`对象尾部添加一个元素。返回该`Set`对象。

`Set.prototype.delete(value)`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;移除`Set`的中与这个值相等的元素，返回`Set.prototype.has(value)`在这个操作前会返回的值（即如果该元素存在，返回`true`，否则返回`false`）。`Set.prototype.has(value)`在此后会返回`false`。

`Set.prototype.clear()`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;移除`Set`对象内的所有元素。

###### demo

```js
let mySet = new Set()

mySet.add(0).add('0').add(null).add(undefined) // Set(4) {0, "0", null, undefined}

mySet.has('0') // true
mySet.has(1) // flase
mySet.has(null) // true

mySet.size // 4

mySet.delete(null) // true, null 从set中移除
mySet.has(null) // false

mySet.size // 3
console.log(mySet) // Set(4) {0, "0", undefined}
```

------

<!-- 迭代Set -->

###### 迭代方法

`Set.prototype.keys()`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回一个新的迭代器对象，该对象包含`Set`对象中的按插入顺序排列的所有元素的值。

`Set.prototype.values()`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回一个新的迭代器对象，该对象包含`Set`对象中的按插入顺序排列的所有元素的值。

`Set.prototype.entries()`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;返回一个新的迭代器对象，该对象包含Set对象中的按插入顺序排列的所有元素的值的[value, value]数组。为了使这个方法和Map对象保持相似， 每个值的键和值相等。

`Set.prototype.forEach(callbackFn[, thisArg])`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;按照插入顺序，为`Set`对象中的每一个值调用一次`callBackFn`。如果提供了`thisArg`参数，回调中的`this`会是这个参数。

###### demo

```js
let mySet = new Set([0, '0', null, undefined])

mySet.keys() // SetIterator {0, "0", null, undefined}

mySet.values() // SetIterator {0, "0", null, undefined}

mySet.entries() // SetIterator {0, "0", null, undefined}
```

------

### 具体实现

在看具体实现前，先看下的工具方法

1、条件进行判断，抛出异常
```js
const assert = (condition, msg) => {
    if (!condition) throw new Error(msg)
}
```

2、isDef, 过滤掉`null`和`undefined`
```js
const isDef = (value) => {
    return value != void 0
}
```

3、isIterable, 简单判断value是否是迭代器对象.
```js
const isIterable = (value) => {
    return isDef(value) && typeof value[Symbol.iterator] === 'function'
}
```

4、
```js
function forOf(obj, cb) {
    let iterable, result

    if (typeof obj[Symbol.iterator] !== "function") throw new TypeError(obj + " is not iterable")
    if (typeof cb !== "function") throw new TypeError('cb must be callable')

    iterable = obj[Symbol.iterator]()

    result = iterable.next()
    while (!result.done) {
        cb(result.value)
        result = iterable.next()
    }
}
```

------

#### 源码
```js
class Set {
    constructor (iterable) {
        this.value = []

        assert(this instanceof Set, 'Constructor Set requires "new"') // 必须使用news来调用

        data && data.forEach(function(item) {
            this.add(item)
        }, this)
    }

    get size() {
        return this.value.length
    }

    has(value) {
        return this.value.includes(value) // 使用includes方法判断是否包含 value MDN: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/includes
    }

    add(value) {
        !this.has(value) && this.value.push(value) // value不存在则添加进数组
        return this
    }

    delete(value) {
        let result = this.has(value)
        result && this.value.splice(this.value.indexOf(value), 1)
        return result
    }

    clear() {
        this.value = [] // 赋值一个空数组
    }
    // 返回一个迭代对象，该对象中的值是Set中的value
    keys () {
        return new Iterator(this.value)
    }

    values () {
        return this.keys()
    }
    // 返回一个迭代对象，不同keys和values的是其值是[value, value]
    entries () {
        return new Iterator(this.value, (value) => [value, value])
    }
    // 返回一个新的迭代器对象，该对象包含Set对象中的按插入顺序排列的所有元素的值。
    [Symbol.iterator] () {
        return this.values()
    }
}
```

### 参考
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)
[ES6 系列之模拟实现一个 Set 数据结构](https://github.com/mqyqingfeng/Blog/issues/91)
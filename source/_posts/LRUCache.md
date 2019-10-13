---
title: LRUCache
date: 2019-03-08 15:03:08
tags:
- js
categories: LRU-Cache 缓存
thumbnail: https://user-images.githubusercontent.com/12566627/66709337-53e57480-ed94-11e9-84dd-3264983a0a6d.jpg
---
前段时间 在项目中使用vue做服务端渲染，并且对渲染出来的页面用 [lru-cache](https://github.com/isaacs/node-lru-cache) 库在前端做了缓存，来减轻服务器压力～。在[Leetcode](https://leetcode-cn.com/problems/lru-cache/)上又遇到了这道题目，在此记录一下～😍

## LRUCache 缓存机制
题目要求：运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。

获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
写入数据 put(key, value) - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。

实现 LRUCache 类：
- LRU 存在容量限制(capacity)
- get(k) 取值 (get)
  + 获取不到返回 -1
  + 获取到则返回，同时把该 (k, v) 对放到最近使用端(removeNode、addNode)


- put(k, v) 存值(put)
    + 如果存在该 k，则删除再在最近使用端添加 (k, v)，如果不存在则在最近使用端添加 (k, v)
    + 如果添加之后总长度大于容量，则删除最远使用的 (k, v)

示例:

```js
LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );

cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // 返回  1
cache.put(3, 3);    // 该操作会使得密钥 2 作废
cache.get(2);       // 返回 -1 (未找到)
cache.put(4, 4);    // 该操作会使得密钥 1 作废
cache.get(1);       // 返回 -1 (未找到)
cache.get(3);       // 返回  3
cache.get(4);       // 返回  4
```

------

### 实现

1、Map 实现

主要利用：`Map的keys() 返回一个新的 Iterator 对象。它包含按照顺序插入 Map 对象中每个元素的key值。`
```js
class LRUCache {
    constructor(capacity = 0) {
        this.cache = new Map()
        this.capacity = capacity
    }

    get(key) {
        const v = this.cache.get(key)
        if (v === undefined) {
            return -1 //不存在返回 -1
        } else {
            this.cache.delete(key)
            this.cache.set(key, v) // 存在则重新del和set，最近最少使用策略
            return v
        }
    }

    put(key, value) {
        if (this.cache.has(key)) {
            this.cache.delete(key)
        } else if (this.cache.size >= this.capacity) { //如果超出size，删掉最少使用项
            this.cache.delete(this.cache.keys().next().value)
        }
        this.cache.set(key, value)
    }
}
```

2、双向链表实现

双向链表节点：
```js
class LinkedListNode {
    constructor(key = 0, value = 0) {
        this.key = key
        this.value = value
        this.prev = null // 当前节点指向的前一个节点
        this.next = null // 当前节点指向的下一个节点
    }
}
```

具体实现
```js
class LRUCache {
    constructor(capacity = 0) {
        this.cache = {}
        this.capacity = capacity
        this.size = 0
        this.head = new LinkedListNode() // 初始化头结点
        this.tail = new LinkedListNode() // 初始化尾结点

        // 初始情况下只有头结点和尾结点
        this.head.next = this.tail // 头结点的 next 指向尾结点
        this.tail.prev = this.head // 尾结点的 prev 指向头结点
    }

    addNode(node) { // 添加节点
        // 当前节点插入第一位
        node.prev = this.head
        node.next = this.head.next
        // 更改原来第一个节点 的指向
        this.head.next.prev = node
        // 最后更换 head 的 next 指向位 node（注意要在最后操作）
        this.head.next = node
    }

    removeNode(node) { // 删除节点
        const prev = node.prev
        const next = node.next

        // 将前一个节点指向 node 的后一个节点
        node.prev.next = next
        // 把后一个节点的 prev 指向 node 的前一个节点
        next.prev = prev
    }

    popTail() { // 删除尾部节点
        const res = this.tail.prev
        this.removeNode(res)
        return res
    }

    moveToHead(node) { //将节点移动到链表头部
        this.removeNode(node)
        this.addNode(node)
    }

    get(key) { //获取
        const node = this.cache[key]
        if (node) {
            // 如果有这个节点，则把这个节点移动到 head
            this.moveToHead(node)
            return node.value
        }
        return -1
    }

    put(key, value) { //插入
        const node = this.cache[key]
        if (!node) { // 不存在该节点
            const newNode = new LinkedListNode(key, value) //创建一个新的节点
            this.cache[key] = newNode //节点 存入cache对象中
            this.addNode(newNode) //插入链表中
            this.size += 1
            if (this.size > this.capacity) { //超过capacity删除尾部节点
                const popNode = this.popTail()
                delete this.cache[popNode.key]
                this.size -= 1
            }
        } else {
            // 存在，则在链表里面先删除这个节点再把这个节点添加到 head
            this.removeNode(node)
            node.value = value //更新val值
            this.addNode(node)
        }
    }
}
```


算法时间复杂度都是 `O(1)`
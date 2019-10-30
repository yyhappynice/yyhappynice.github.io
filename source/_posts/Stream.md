---
title: Stream
date: 2018-12-25 09:03:34
tags: js
categories: node 中的流 Stream
thumbnail: https://user-images.githubusercontent.com/12566627/66715019-cc7a1e80-edf0-11e9-8903-79b27e2165ac.jpg
---
流（stream）是 Node.js 中处理流式数据的抽象接口。 stream 模块用于构建实现了流接口的对象。Node.js 提供了多种流对象。 例如，[HTTP](http://nodejs.cn/api/http.html#http_class_http_incomingmessage) 服务器的请求和 [process.stdout](http://nodejs.cn/api/process.html#process_process_stdout) 都是流的实例。流可以是可读的、可写的、或者可读可写的。 所有的流都是 [EventEmitter](http://nodejs.cn/api/events.html#events_class_eventemitter) 的实例。

### 一、流的起源
流的理念源于Unix中的 Pipeline ，简单地说，就是将上一个程序的输出可以作为下一个程序的输入。数据在不同的程序之间流动，就像水流在水管中一样，我们可以通过管道符 __|__ 把各个程序连接起来 (node中使用pipe) ，为数据的流动提供管道。例如：
```bash
ps -ef | grep node
```

### 二、node 中 stream 模块

在 node 中有四种基本的流类型：

Readable - 用来读取数据，例如 fs.createReadStream()
Writable - 可写入数据的流，例如 fs.createWriteStream()
Duplex - 可读又可写的流，例如 net.Socket
Transform - 在读写过程中可以修改或转换数据的 Duplex 流（例如 zlib.createDeflate()）

#### Readable Stream

可读流 (Readable Stream) 事实上存在下面两种工作模式：flowing 和 paused 。也正是因为有这两种机制的原因，使我们能够驱动数据流动。

> flowing：在该模式下，会尽快获取数据向外输出。因此如果没有事件监听，也没有pipe()来引导数据流向，数据可能会丢失。
paused：默认模式。在该模式下，需要手动调用stream.read()来获取数据。

所有初始工作模式为 paused 的 Readable 流，我们可以通下面三种途径切换到 flowing 模式：
<!-- 我们可以通过三种途径切换到 flowing 流动模式: -->
> 1、监听 'data' 事件
2、调用 pipe() 数据发送到 Writable
3、调用 resume()

```js
const fs = require('fs')
const path = require('path')
const rs = fs.createReadStream(path.join(__dirname, './1.txt'))

rs.on('data', (data) => {
    console.log(data)
})
// 或 rs.pipe(process.stdout) 或 rs.resume()
```
这个模式的流程图 (网络图片侵删) 如下：
![flowing](https://gw.alicdn.com/tfs/TB1rJvJaMMPMeJjy1XbXXcwxVXa-742-413.png)
首先，流会先根据 `highWatermark` 判断，缓存池的水位是否超过了这个标记的阈值，超过 push 的时候会返回 false，否则会将数据存储到内部的缓冲器（buffer）中，当 push() 返回 false 也就是我们常说的背压机制



同样可读流也可以通过下面途径切换到 paused 模式：
> 1、如果不存在管道目标（pipe destination），可以通过调用 stream.pause() 方法实现。
2、如果存在管道目标，可以通过取消 'data' 所有的事件监听，并调用 stream.unpipe() 方法移除所有管道目标来实现。

```js
const fs = require('fs')
const path = require('path')
const rs = fs.createReadStream(path.join(__dirname, './1.txt'))

rs.on('readable', () => {
    let d = rs.read(1)
    console.log(d)
})
```

可读流中有一个标记 flowing (默认值为 null) 存在三种状态




### 参考
[深入理解 Node.js Stream 内部机制](https://fed.taobao.org/blog/2017/09/01/nodejs-stream/)
[123](https://juejin.im/post/5c77b46df265da2dea053df5?utm_source=gold_browser_extension)


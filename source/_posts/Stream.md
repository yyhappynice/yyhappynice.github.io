---
title: Stream
date: 2018-12-25 09:03:34
tags: js
categories: node 中的流 Stream
thumbnail: https://user-images.githubusercontent.com/12566627/66715019-cc7a1e80-edf0-11e9-8903-79b27e2165ac.jpg
---
流（stream）是 Node.js 中处理流式数据的抽象接口。 stream 模块用于构建实现了流接口的对象。Node.js 提供了多种流对象。 例如，[HTTP](http://nodejs.cn/api/http.html#http_class_http_incomingmessage) 服务器的请求和 [process.stdout](http://nodejs.cn/api/process.html#process_process_stdout) 都是流的实例。流可以是可读的、可写的、或者可读可写的。 所有的流都是 [EventEmitter](http://nodejs.cn/api/events.html#events_class_eventemitter) 的实例。

### 一、流的起源
流的理念源于Unix中的Pipeline，简单地说，就是将上一个程序的输出可以作为下一个程序的输入。数据在不同的程序之间流动，就像水流在水管中一样，我们可以通过管道符 __|__ 把各个程序连接起来，为数据的流动提供管道。例如：
```bash
ps -ef | grep node
```

### 二、node 中 stream 模块

在node中流分为四种：[Readable]()
[123](https://juejin.im/post/5c77b46df265da2dea053df5?utm_source=gold_browser_extension)


### 参考
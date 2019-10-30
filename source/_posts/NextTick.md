---
title: nextTick
date: 2019/02/24 16:48:08
tags:
- js
categories: vue-nextTick 机制
thumbnail: https://user-images.githubusercontent.com/12566627/66709322-0832cb00-ed94-11e9-90c5-b61989f1baff.jpg
---
在 Vue 中，是[异步执行 DOM 更新的](https://cn.vuejs.org/v2/guide/reactivity.html#%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0%E9%98%9F%E5%88%97)，当观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据可以避免不必要的计算和 DOM 的重复渲染；在vue开发中我们也常常会使用到 nextTick，让我们来一探究竟吧～😊

## vue nextTick机制

### 事件循环（Event Loop）
在了解原理之前我们先看下 js 的事件循环 [Event Loop](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)：

JS中有两种任务类型：微任务（microtask）和宏任务（macrotask），在ES6中，microtask称为 jobs，macrotask称为 task。

__宏任务__： `script` （整体代码）、`setTimeout` 、`setInterval` 、`setImmediate` 、`I/O` 、`UI rendering`

__微任务__：`process.nextTick`（Nodejs） 、`promise` 、`Object.observe` 、`MutationObserver`

一次事件循环的步骤包括：
1、从macrotask queue中取出最早的任务（通常是script整体代码）执行，microtask 压入到microtask queue中，macrotask 压入到macrotask queue中
2、从macrotask queue中删除执行过的macrotask
3、检查是否存在 Microtask，如果存在则不停的执行，直至清空 microtask 队列
4、更新render(每一次事件循环，浏览器都可能会去更新渲染)
5、重复以上步骤

![event-loop](https://user-images.githubusercontent.com/12566627/66882038-7f1dcd00-effb-11e9-8197-50808c4116d2.png)

用一段代码表示事件循环如下：
```js
for (macroTask of macroTaskQueue) {
    // 1. Handle current MACRO-TASK
    handleMacroTask();

    // 2. Handle all MICRO-TASK
    for (microTask of microTaskQueue) {
        handleMicroTask(microTask);
    }
}
```
------
### Vue nextTick 的实现[（2.6）](https://github.com/vuejs/vue/blob/dev/src/core/util/next-tick.js)
nextTick的实现，主要是要想在 microTask 列队中增加 function，在当前栈执行完成后执行 nextTick 传入的 funtion，源码如下：

```js
import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

const callbacks = []
let pending = false // 标记位，如果已经有 timerFunc 被推送到任务队列中去则不需要重复推送

function flushCallbacks () {
    pending = false //重置 pending 状态
    const copies = callbacks.slice(0) //浅拷贝 callbacks 数组
    callbacks.length = 0
    for (let i = 0; i < copies.length; i++) {
        copies[i]()
    }
}
// 在2.5版本中组合使用microtasks 和macrotasks，但是重绘的时候还是存在一些小问题，
// 而且使用macrotasks在任务队列中会有几个特别奇怪的行为没办法避免，So在任何地方优先使用microtasks 。
// Here we have async deferring wrappers using microtasks.
// In 2.5 we used (macro) tasks (in combination with microtasks).
// However, it has subtle problems when state is changed right before repaint
// (e.g. #6813, out-in transitions).
// Also, using (macro) tasks in event handler would cause some weird behaviors
// that cannot be circumvented (e.g. #7109, #7153, #7546, #7834, #8109).
// So we now use microtasks everywhere, again.
// A major drawback of this tradeoff is that there are some scenarios
// where microtasks have too high a priority and fire in between supposedly
// sequential events (e.g. #4521, #6690, which have workarounds)
// or even between bubbling of the same event (#6566).
let timerFunc

// The nextTick behavior leverages the microtask queue, which can be accessed
// via either native Promise.then or MutationObserver.
// MutationObserver has wider support, however it is seriously bugged in
// UIWebView in iOS >= 9.3.3 when triggered in touch event handlers. It
// completely stops working after triggering a few times... so, if native
// Promise is available, we will use it:
/* istanbul ignore next, $flow-disable-line */
if (typeof Promise !== 'undefined' && isNative(Promise)) { //如果原生支持 则使用 Promise
    const p = Promise.resolve()
    timerFunc = () => {
        p.then(flushCallbacks)
        // In problematic UIWebViews, Promise.then doesn't completely break, but
        // it can get stuck in a weird state where callbacks are pushed into the
        // microtask queue but the queue isn't being flushed, until the browser
        // needs to do some other work, e.g. handle a timer. Therefore we can
        // "force" the microtask queue to be flushed by adding an empty timer.
        if (isIOS) setTimeout(noop) //在ios下添加空的计时器，来刷新 microtask 列队
     }
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
    isNative(MutationObserver) ||
    // PhantomJS and iOS 7.x
    MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
    // Use MutationObserver where native Promise is not available,
    // e.g. PhantomJS, iOS7, Android 4.4
    // (#6466 MutationObserver is unreliable in IE11)
    let counter = 1
    const observer = new MutationObserver(flushCallbacks)
    const textNode = document.createTextNode(String(counter))
    observer.observe(textNode, {
        characterData: true
    })
    timerFunc = () => {
        counter = (counter + 1) % 2
        textNode.data = String(counter)
    }
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
    // Fallback to setImmediate.
    // Technically it leverages the (macro) task queue,
    // but it is still a better choice than setTimeout.
    timerFunc = () => {
        // 如果不支持Promise/MutationObserver，则使用setImmediate(macrotask)兼容，比setTimeout更好
        // setImmediate 可以保证让代码在下一次事件循环执行[参考](https://www.cnblogs.com/onepixel/articles/7605465.html)
        setImmediate(flushCallbacks)
    }
} else {
    // Fallback to setTimeout.
    timerFunc = () => {
        setTimeout(flushCallbacks, 0)
    }
}

export function nextTick (cb?: Function, ctx?: Object) { //cb 回掉函数，ctx执行上下文
    let _resolve
    callbacks.push(() => { // 收集所有的回调函数
        if (cb) {
            try {
                cb.call(ctx)
            } catch (e) {
                handleError(e, ctx, 'nextTick')
            }
        } else if (_resolve) {
            _resolve(ctx)
        }
    })
    if (!pending) {
        pending = true
        timerFunc() // 创建microtask，异步执行flushCallbacks函数
    }
    // 当 nextTick 没有传入函数参数的时候，返回一个 Promise 调用
    if (!cb && typeof Promise !== 'undefined') {
        return new Promise(resolve => {
            _resolve = resolve
        })
    }
}
```
总结：源码看下来首先将所有的回调收集到 callbacks 数组中，调用 timerFunc() 函数(优先使用 Promise > MutationObserver > setImmediate > setTimeout 来创建异步任务) 创建异步任务延迟调用 flushCallbacks，来遍历执行 callbacks 数组中的所有函数。

------
### Watcher队列
在了解 nextTick 之后，我们看下 dom 为什么是异步更新的，当我们更改 this.message 值的时候，直接打印 textContent 值dom其实是没有更新的，在 this.$nextTick 中打印，dom才更新完成
```js
Vue.component('example', {
    template: '<span>{{ message }}</span>',
    data: function () {
        return {
        message: '没有更新'
        }
    },
    methods: {
        updateMessage: function () {
            this.message = '更新完成'
            console.log(this.$el.textContent) // => '没有更新'
            this.$nextTick(function () {
                console.log(this.$el.textContent) // => '更新完成'
            })
        }
    }
})
```
简单分析一下 `this.message = '更新完成'` 赋值操作后，vue 派发更新的过程 如下：
首先 vue 在响应式数据依赖收集过程中，使用 Object.defineProperty 在 get 的时候使用 [dep.depend()](https://github.com/vuejs/vue/blob/dev/src/core/observer/index.js#L163) 做依赖收集，当触发 set 操作的时候，会调用 [dep.notify()](https://github.com/vuejs/vue/blob/dev/src/core/observer/index.js#L191) 方法通知所有的订阅者。

下面我们看一下 Dep 构造函数，定义在 [src/core/observer/dep.js](https://github.com/vuejs/vue/blob/dev/src/core/observer/dep.js) 中：

```js
class Dep {
    static target: ? Watcher
    //...
    addSub (sub: Watcher) {
        this.subs.push(sub) //将 Watcher 添加到 this.subs 数组中
    }

    depend () {
        if (Dep.target) {
            Dep.target.addDep(this)
        }
    }
    notify () {
        // stabilize the subscriber list first
        const subs = this.subs.slice()
        for (let i = 0, l = subs.length; i < l; i++) {
            subs[i].update()
        }
    }
}

Dep.target = null
const targetStack = []

export function pushTarget (target: ?Watcher) {
    targetStack.push(target)
    Dep.target = target
}
```
首先，我们注意到 Dep 下有个 target 静态属性 类型是 Watcher，当调用 dep.depend() 的时候会调用 Watcher 下的 addDep(this); dep.notify 函数很简单中主要是遍历所有的 subs，也就是 Watcher 的实例数组，并调用 update() 方法，下面我们看一下 Watcher 构造函数中，这两个函数的实现：
```js
class Watcher {
    //...
    get () {
        pushTarget(this) // 当在 new Watcher 时候会触发，将 this 挂载到 Dep.target 上
        //...
    }

    addDep (dep: Dep) {
        //...
        dep.addSub(this) // 调用 dep 下的 addSub
    }
    update () {
         /* istanbul ignore else */
        if (this.lazy) {
            this.dirty = true
        } else if (this.sync) {
            this.run()
        } else {
            queueWatcher(this)
        }
    }
}
```
在 Watcher 构造函数中的 addDep 调用 dep.addSub(this) 将当前的 Watcher 实例push到 dep 下 this.subs 数组中，在遍历数组调用 update 最终执行 queueWatcher(this) 方法。
__注意：__ 在 queueWatcher 函数中，执行了 [nextTick(flushSchedulerQueue)](https://github.com/vuejs/vue/blob/dev/src/core/observer/scheduler.js#L187)，同样使用 nextTick 将 flushSchedulerQueue 这个任务放到异步列队中。这个函数中调用了 [watcher.run()](https://github.com/vuejs/vue/blob/dev/src/core/observer/scheduler.js#L95) （watcher.run() 中 调用 this.getAndInvoke 新老的 value 对比）来的更新视图。正是这里使用了 nextTick 来更新 dom， 所以我们想获取 dom 更新后的值，需要在 this.$nextTick 回调中才可以拿到。

关于为什么 vue 视图更新是异步的 和 nextTick 实现原理今天就介绍到这啦～ 😊


## 参考
[js事件循环机制](http://lynnelv.github.io/js-event-loop-browser)
[你真的理解$nextTick么](https://juejin.im/post/5cd9854b5188252035420a13)
[Vue.js 技术揭秘](https://ustbhuangyi.github.io/vue-analysis/reactive/setters.html#%E8%BF%87%E7%A8%8B%E5%88%86%E6%9E%90)
[从Vue.js源码看异步更新DOM策略及nextTick](https://juejin.im/post/59c7b25a5188257a125d7a98)
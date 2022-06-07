---
title: Tasks, Microtasks, Queues and Schedules JS运行机制
date: 2022-06-06 01:00:00
tags:
- JavaScript
---

# Tasks, Microtasks, Queues and Schedules:JS运行机制

```jsx
console.log('script start');

setTimeout(function() {

  console.log('setTimeout');

}, 0);

Promise.resolve().then(function() {

  console.log('promise1');

}).then(function() {

  console.log('promise2');

});

console.log('script end');
```

[Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)

# 练习题

```jsx
const EventEmitter = require('events')

class EE extends EventEmitter {}

const yy = new EE()

console.log('测试开始')

yy.on('event', () => console.log('我是EventEmitter触发的事件回调'))

setTimeout(() => {

  console.log('0 毫秒后到期的定时器回调1')

  process.nextTick(() => console.log('我是0毫秒定时器1加塞的一个微任务'))

}, 0)

setTimeout(() => {

  console.log('0 毫秒后到期的定时器回调2')

  process.nextTick(() => console.log('我是0毫秒定时器2加塞的一个微任务'))

}, 0)

setImmediate(() => console.log('immediate 立即回调'))

process.nextTick(() => console.log('process.nextTick 的第一次回调'))

new Promise((resolve) => {

  console.log('我是promise')

}).then(() => {

  yy.emit('event')

  process.nextTick(() => console.log('process.nextTick 的第二次回调'))

  console.log('promise 第一次回调')

})

.then(() => console.log('promise 第二次回调'))

console.log('测试结束?')

/* 打印结果如下：

  测试开始

  我是promise

  测试结束?

  process.nextTick 的第一次回调

  0 毫秒后到期的定时器回调1

  我是0毫秒定时器1加塞的一个微任务

  0 毫秒后到期的定时器回调2

  我是0毫秒定时器2加塞的一个微任务

  immediate 立即回调

*/
```

# 总结

js把异步任务队列分为两种：宏任务(macro task)和微任务(micro task)，二者的区别是执行时机的不同。

- **先执行微任务的队列，再检查宏任务的队列**
- **在当前的微任务没有执行完成时，是不会执行下一个宏任务的。**
- **每次执行完一个宏任务之后，要检查微任务队列是否又有任务需要执行了(这个体现在上面的练习题中的超时后加塞的微任务队列)**

那么知道了执行的机制之后，剩下的一个问题就是任务类型的划分，整理如下一表，结合上面的问题，相信你心中有了答案了~

*Tips*

1. async函数在await之前的代码都是同步执行的，可以理解为await之前的代码属于new Promise时传入的代码，await之后的所有代码都是在Promise.then中的回调
2. **node11版本之前的打印和这里的不大一样，原因可以看这里的[MacroTask and MicroTask execution order](https://github.com/nodejs/node/issues/22257)**


|事件	| 宏任务/微任务	   |浏览器	|nodejs|
|:-----------|:-----------|:-----------:|:-----------:|
|I/O	| 宏任务	       |✅	|✅|
|setTimeout	| 宏任务	       |✅	|✅|
|setInterval	| 宏任务	       |✅	|✅|
|setImmediate	| 宏任务	       |❌	|✅|
|requestAnimationFrame	| 宏任务	       |✅	|❌|
|process.nextTick	| 微任务	       |✅	|✅|
|MutationObserver	| 微任务	       |✅	|❌|
|Promise.then catch finally	| 微任务	       |✅	|✅|
| EventEmitter	             | 微任务	       |❌	|✅|

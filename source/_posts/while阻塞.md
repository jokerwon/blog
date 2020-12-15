---
title: Promise 控制 while 循环时需要注意的问题以及阻塞 while 的方案
---

## 问题背景

利用 Promise 阻塞循环导致了死循环。

<!-- more -->

~~~js
function test() {
  function delay(interval) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve()
      }, interval * 1000)
    })
  }
  let x = 5
  while(x > 0) {
    console.log('testing...')
    delay(1).then(function () {
      x--
      console.log(x)
    })
  }
}
test()
~~~

预期结果：// TODO

实际结果：// TODO

## 问题分析

函数 delay() 会返回一个 Promise 对象，该对象的状态会在延时指定时间后变为 fullfilled。

~~~js
let x = 5
while(x > 0) {
  console.log('testing...')
  delay(1).then(function () {
    x--
    console.log(x)
  })
}
~~~

运行代码后，控制台会一直打印 “testing...”，这是由于此时的 while 循环变成了一个死循环。那么，是什么原因导致的呢？

我们看代码块的第四行，delay() 产生了一个 promise 对象，在 1s 后执行 then() 方法中的匿名函数，来操作 x--。关键的问题就显现出来了，此处 while 循环是由 x 的值来控制，但是操作 x 的地方却在微任务队列中，意思就是说，在当前的事件环中，宏任务队列的 x 永远不会改变，那么当前的 while 循环也就永远不会结束，这就是导致产生死循环的直接原因。

## while 循环阻塞方案

### 利用假死循环

1. while 版

   ~~~js
   function sleepByWhile(delay) {  // seconds
     let start = (new Date()).getTime();
     while ((new Date()).getTime() - start < delay * 1000) {
       continue;
     }
   }
   ~~~

2. for 版

   ~~~js
   function sleepByFor(delay) {
     for (let t = Date.now(); Date.now() - t <= delay * 1000;);
   }
   ~~~

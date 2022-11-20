---
title: 《Vue.js设计与实现》笔记
tag: [Vue, JavaScript, Vue源码, 笔记]
---



### 第一章 权衡的艺术

<!-- more -->



##### 1.1 命令式和声明式



##### 1.2 性能与可维护性的权衡

声明式代码的性能不优于命令式性能的代码。

声明式代码的更新性能消耗 = 找出差异的性能消耗 + 直接修改的性能消耗。

很难写出绝对优化的命令式代码。



##### 1.3 虚拟DOM的性能

性能差 --> 性能高

|          | innerHTML(模板) | 虚拟DOM | 原生 JavaScript |
| -------- | --------------- | ------- | --------------- |
| 心智负担 | 中等            | 小      | 大              |
| 可维护性 |                 | 强      | 差              |
| 性能     | 差              | 不错    | 高              |



##### 1.4 运行时和编译时

纯运行时：无法分析用户提供的内容。

运行时 + 编译时：编译时可以分析用户提供内容，并在运行时做进一步优化。[`Vue`](https://cn.vuejs.org/)

纯编译时：可以分析用户内容，性能更高，但灵活性不够好。[` Svelte `](https://www.sveltejs.cn/)



### 第二章 框架设计的核心要素



##### 2.1 提升用户的开发体验

衡量一个框架是否足够优秀的指标之一就是开发体验。



##### 2.2 控制框架代码的体积

框架的大小也是衡量标准之一。

在开发环境中为用户提供友好的警告信息的同时，不会增加生产环境代码的体积。



##### 2.3 框架要做到良好的 Tree-Shaking

###### 什么是 Tree-Shaking？

消除永远不会被执行的代码，也就是排除 dead code。

###### 如何实现 Tree-Shaking？

- 模块必须是 ESM(ES Module)。因为 Tree-Shaking 依赖 ESM 的静态结构。

- 函数调用不会产生副作用。通常产生副作用的代码都是模块内函数的顶级调用。

  ~~~javascript
  foo() // 顶级调用
  
  function bar() {
    foo() // 函数内调用
  }
  ~~~



##### 2.4 框架应该输出怎样的构建产物

- 直接可以在 HTML 中引用，则输出 IIFE(立即调用函数表达式)。
- 如果浏览器支持原生 ESM，可以以 `<script type="module" />` 的形式引用，则输出 EMS 格式资源。
- 可以在 Node 环境中运行，则输出 cjs(CommonJS)格式资。



##### 2.5 特性开关

用户可以自行开关框架提供的特性。

- 对于用户关闭的特性，可以利用 Tree-Shaking 让其不包含在最终的资源中。
- 该机制为框架设计带来了灵活性，可以通过特性开关任意为框架添加新的特性，而不用担心资源体积变大。框架升级时，可以通过特性开关来支持遗留 API，这样新用户可以选择不使用遗留 API 来使打包的资源体积最小化。



##### 2.6 错误处理

框架错误处理机制的好坏直接决定用户应用程序的健壮性。



##### 2.7 良好的 TypeScript 类型支持

TS 类型支持也是评价框架的重要指标。

**使用 TS 编写框架** 和 **对 TS 类型支持友好** 是两件**完全不同**的事。



### 第三章 Vue.js 3 的设计思路



从全局视角了解 Vue.js 3 的设计思路、工作机制及其重要组成部分。



##### 3.1 声明式地描述 UI

###### 使用模板和 JavaScript 对象描述 UI 有何不同？

使用 JavaScript 对象描述 UI 更加灵活。

`h` 函数的返回值就是一个对象，其作用是让用户编写虚拟 DOM 更加轻松。所以 `h` 函数就是一个辅助创建虚拟 DOM 的工具函数。

Vue 会根据组件的 `render` 函数(渲染函数)的返回值拿到虚拟 DOM，然后渲染组件内容。



##### 3.2 初识渲染器

渲染器的作用就是把虚拟 DOM 渲染为真实DOM。

![渲染器](https://cdn.jsdelivr.net/gh/jokerwon/images/img/202204032210326.png)



### 第四章 响应系统的作用与实现



##### 4.1 响应式数据与副作用函数

副作用函数指的是会产生副作用的函数。副作用函数的执行会直接或间接影响其他函数的执行。

~~~js
// 全局变量
let val = 1

function effect() {
  val = 2 // 修改全局变量，产生副作用
}
~~~



##### 4.2 响应式数据的基本实现

~~~js
const obj = { text: 'hello world' }
function effect() {
  document.body.innerText = obj.text
}

obj.text = "hello vue3" // 修改 obj.text 的值，同时希望副作用函数会重新执行
~~~

实现思路：

1. 当读取字段 `obj.text` 的时候，把副作用函数 `effect` 存储起来。
2. 当设置 `obj.text` 时，把副作用函数 `effect` 取出并执行。

~~~js
// 存储副作用函数
const bucket = new Set()

// 原始数据
const data = { text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 effect 添加到 bucket 中
    bucket.add(effect)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 取出副作用函数并执行
    bucket.forEach(fn => fn())
    // 返回 true 表示设置成功
    return true
  }
})
~~~

> ###### 说明
>
> 后续响应式系统的章节都以该系统为基础，笔记中只会记录存在的问题、可以优化的地方、新的需求和对应的解决方案。



##### 4.3 设计一个完善的响应式系统

###### 存在的问题

1. 当前副作用函数的名字被硬编码为 `effect` 。

2. 没有在副作用函数与被操作的目标字段之间建立明确的联系。

   表现：修改无关字段时也会引起 `effect` 函数重新执行。

###### 解决方案

1. 使用一个全局变量存储被注册的副作用函数。[修改记录](https://gitee.com/jokerwon/my-vue/commit/e00166dd0055abcd8f27ed9380b4be4118830fe3)

   ~~~js
   // 用一个全局变量存储被注册的副作用函数
   let activeEffect = null;
   
   function effect(fn) {
       // 当调用 effect 注册副作用函数时，将副作用函数 fn 赋值给 activeEffect
       activeEffect = fn;
       fn();
   }
   
   // 注册副作用函数
   effect(() => {
       document.body.innerHTML = obj.text;
   });
   ~~~

2. 修改用于存储副作用容器的数据结构。[修改记录](https://gitee.com/jokerwon/my-vue/commit/599e7c5fe4383a6719505f7819585c5b5248bb40)

   ~~~js
   /**
    * @description 存储副作用函数的桶
    * bucket 
    *   类型 WeakMap<Target, Map<string, Set<Effect>>>
    *   结构：
    *   |--> target1
    *         |--> property1
    *               |--> effectFn1
    *         |--> property2
    *               |--> effectFn1
    *               |--> effectFn2
    *   |--> target2
    *         |--> property3
    *               |--> effectFn3
    *               |--> effectFn4
    */
   const bucket = new WeakMap();
   
   const obj = new Proxy(data, {
       // 拦截读取操作
       get(target, key) {
           if (!activeEffect) {
               return target[key];
           }
   
           // 根据 target 获取 depsMap, 它是一个 Map<key, Set<Effect>>
           let depsMap = bucket.get(target);
           // 如果 depsMap 不存在，则创建一个新的 Map 与 target 关联
           if (!depsMap) {
               bucket.set(target, (depsMap = new Map()));
           }
   
           // 再根据 key 从 depsMap 中获取 deps，它是一个 Set<Effect>
           // 里面保存着所有与当前 key 关联的副作用函数
           let deps = depsMap.get(key);
           // 如果 deps 不存在，同样新建一个 Set 与 key 关联
           if (!deps) {
               depsMap.set(key, (deps = new Set()));
           }
           // 将当前激活的副作用函数添加到 deps 中
           deps.add(activeEffect);
   
           // 返回当前 key 的值
           return target[key]
       },
       // 拦截赋值操作
       set(target, key, newVal) {
           // 设置属性值
           target[key] = newVal;
   
           // 根据 target 获取 depsMap，它是 key --> effects
           const depsMap = bucket.get(target);
           if (!depsMap) return true
   
           // 根据 key 从 depsMap 中获取对应的所有 effects
           const effects = depsMap.get(key);
           // 执行副作用函数
           effects && effects.forEach(effect => effect());
   
           // 返回 true 表示赋值成功
           return true;
       }
   });
   ~~~

   

##### 4.4 分支切换与 cleanup

###### 分支切换

~~~js
const data = { ok: true, text: 'hello world' }
const obj = new Proxy(data, {/*...*/})

effect(function effectFn() {
  document.body.innerText = obj.ok ? obj.text : 'not'
})
~~~

当字段 `obj.ok` 变化时，代码执行的分支也会跟着变化，这就是所谓的分支切换。

###### 分析问题

~~~js
// 开始时 obj.ok === true
// data 此时的依赖树结构是
data
  |--> ok
        |--> effectFn
  |--> text
        |--> effectFn

// 当修改 obj.ok === false
// data 此时的依赖树结构并没有发生变化，text 对应的副作用函数会被遗留下来
// 但是此时理想的依赖树结构是
data
  |--> ok
        |--> effectFn
~~~

当 `obj.ok` 从 `true` 变为  `false` 后，修改 `obj.text` 依然会导致 `effectFn` 执行。

###### 解决方案

每次副作用执行时，先把它从所有与之关联的依赖集合中删除。[修改记录](https://gitee.com/jokerwon/my-vue/commit/5e9ec4e6da72f96bc5ddf40e535f78063e6f5d2c)

~~~js
function effect(fn) {
    // 真实的副作用函数
    const effectFn = () => {
	      // >>>>>>> 新增
        // 调用 cleanup 函数完成清除工作
        cleanup(effectFn);
	      // >>>>>>>
        activeEffect = effectFn;
        fn();
    }
    
    // >>>>>>> 新增
    // activeEffect.deps 用来存储所有与该副作用函数相关联的依赖集合，Array<Set<Effect>>
    effectFn.deps = [];
	  // >>>>>>>
    effectFn();
}
// >>>>>>> 新增
function cleanup(effectFn) {
    if (!effectFn.deps) return;
    for (let i = 0; i < effectFn.deps.length; i++) {
        // deps 是一个包含了 effectFn 的依赖集合
        const deps = effectFn.deps[i];
        // 将 effectFn 从这个依赖集合中移除
        deps.delete(effectFn);
    }
    // 最后需要重置 effectFn.deps 数组
    effectFn.deps.length = 0;
}
// >>>>>>>

function track(target, key) {
    if (!activeEffect) return;
    let depsMap = bucket.get(target);
    if (!depsMap) {
        bucket.set(target, (depsMap = new Map()));
    }
    // 再根据 key 从 depsMap 中获取 deps，它是一个 Set<Effect>
    // 里面保存着所有与当前 key 关联的副作用函数
    let deps = depsMap.get(key);
    if (!deps) {
        depsMap.set(key, (deps = new Set()));
    }
    // 将当前激活的副作用函数添加到 deps 中
    deps.add(activeEffect);

 		// >>>>>>> 新增
    // deps 就是一个与当前副作用函数存在联系的依赖集合
    // 将其添加到 activeEffect.deps 中，这样就完成了 activeEffect 和 deps 的双向关联
    activeEffect.deps.push(deps);
	  // >>>>>>>
}

function trigger(target, key) {
    const depsMap = bucket.get(target);
    if (!depsMap) return;
    const effects = depsMap.get(key);

	  // >>>>>>> 新增
    // 复制一份 effects，避免遍历时 effects 被增删而导致死循环
    const effectsToRun = new Set(effects);
    // 执行副作用函数
    effectsToRun.forEach(effect => effect());
	  // >>>>>>>
}
~~~



##### 4.5 嵌套的 effect 与 effect 栈

###### 新需求

支持 effect 嵌套。

~~~js
const data = { bar: true, foo: true }
const obj = new Proxy(data, {/* ... */})

let temp1, temp2;

// effectFn1 嵌套了 effectFn2
effect(function effectFn1() {
    console.log('effectFn1 执行');

    effect(function effectFn2() {
        console.log('effectFn2 执行');
        temp2 = obj.bar;
    });

    temp1 = obj.foo;
});

obj.foo = false;
~~~

###### 需求分析

依赖关系

~~~js
data
 |--> bar
       |--> effectFn2
 |--> foo
       |--> effectFn1
~~~

期待输出

~~~js
effectFn1 执行
effectFn2 执行
effectFn1 执行
effectFn2 执行
~~~

实际输出

~~~js
effectFn1 执行
effectFn2 执行
effectFn2 执行
~~~

原因分析

`effectFn2` 执行完后 `activeEffect` 依然指向 `effectFn2` 。

###### 解决方案

创建一个副作用函数栈 `effectStack` ，在副作用执行时，将当前副作用函数压栈，待副作用函数执行完毕后将其出栈，并始终让 `activeEffect` 指向栈顶元素。[修改记录](https://gitee.com/jokerwon/my-vue/commit/7af26a27b7ee8342476876cb423eaf447621715c)

~~~js
let activeEffect = null
// effect 栈
const effectStack = []
function effect(fn) {
    const effectFn = () => {
        cleanup(effectFn);
        activeEffect = effectFn;
	      // >>>>>>> 新增
        // 在调用前压栈
        effectStack.push(effectFn);
        fn();
        // 调用完毕后出栈
        effectStack.pop();
        // 并更新 activeEffect 指向栈顶的副作用函数
        activeEffect = effectStack[effectStack.length - 1];
	      // >>>>>>>
    }
    effectFn.deps = [];
    effectFn();
}
~~~



##### 4.6 避免无限递归循环

###### 存在的问题

~~~js
const data = { foo: 1 }
const obj = new Proxy(data, {/* ... */})

effect(() => obj.foo++)
~~~

上述代码执行会引起栈溢出

~~~js
Uncaught RangeError: Maximum call stack size exceed
~~~

###### 分析问题

`obj.foo = obj.foo + 1` 既会读取，也会设置 `obj.foo` 。因此执行流程会变成 `track` => `trigger`  => `trigger` => ... 无限递归的调用 `trigger` 函数。

###### 解决方案

如果 `trigger` 触发执行的副作用函数与当前正在执行的副作用函数相同，则不触发执行。[修改记录](https://gitee.com/jokerwon/my-vue/commit/6c3ae2b3d79d88424429d1e4104389ef83a45d6a)

~~~js
function trigger(target, key) {
    const depsMap = bucket.get(target);
    if (!depsMap) return;
    const effects = depsMap.get(key);
    const effectsToRun = new Set();
  	// >>>>>>> 新增
    effects && effects.forEach(effect => {
        // 如果 trigger 触发执行的副作用函数与当前正在执行的副作用函数相同，则不触发执行。
        if (effect !== activeEffect) {
            effectsToRun.add(effect)
        }
    });
	  // >>>>>>>
    // 执行副作用函数
    effectsToRun.forEach(effect => effect());
}
~~~



##### 4.7 调度执行

###### 新需求

可调度。当 `trigger` 动作触发副作用函数执行时，有能力决定副作用函数执行的时机、次数以及方式。

###### 需求分析

~~~js
const data = { foo: 1 }
const obj = new Proxy(data, {/* ... */})
effect(() => console.log(obj.foo))

obj.foo++

console.log('over')
~~~

当前结果

~~~
1
2
over
~~~

期待结果

~~~
1
over
2
~~~

###### 解决方案

为 `effect` 函数设计一个选项参数 `options` ，允许用户指定调度器。[修改记录](https://gitee.com/jokerwon/my-vue/commit/c4d138b0f98b0ce524c01f2f08351b4629619471)

~~~js
function effect(fn, options = {}) {
    // ... 省略
  
    // 将 options 挂载到 effectFn 上
    effectFn.options = options;
    effectFn.deps = [];
    effectFn();
}

function trigger(target, key) {
		// ...省略

    // 执行副作用函数
    effectsToRun.forEach(effect => {
        // 如果一个副作用函数存在调度器，则调用调度器，并将其作为参数传入
        if (effect.options && effect.options.scheduler) {
            effect.options.scheduler(effect);
        } else {
            effect();
        }
    });
}

// 这样使用
effect(() => {
    console.log(obj.foo);
}, {
    scheduler(fn) {
        setTimeout(fn, 0);
    }
});
obj.foo++;
~~~

###### 引申: 实现简易调度器合并多次重复更新

~~~js
// 定义一个任务队列
const jobQueue = new Set();
// 创建一个 promise 实例，利用它将 任务队列的执行 添加到微任务队列
const p = Promise.resolve();

// 代表是否正在刷新队列的标志，避免任务队列重复执行
let isFlushing = false;
function flushJob() {
  // 任务队列刷新时无需再刷新
  if (isFlushing) return;
  // 设置为 true，代表正在刷新
  isFlushing = true;
  p.then(() => {
    jobQueue.forEach((job) => job());
  }).finally(() => {
    // 结束后重置 isFlushing
    isFlushing = false;
  });
}

// 使用
effect(
  () => {
    console.log(obj.foo);
  },
  {
    scheduler(fn) {
      // 每次调度时将副作用函数添加到 jobQueue 队列中
      jobQueue.add(fn);
      // 调用 flushingJob 刷新队列
      flushJob();
    },
  }
);
obj.foo++;
obj.foo++;
~~~



##### 4.8 计算属性 computed 与 lazy

###### 新需求

1. 懒执行的 `effect` 。
2. 计算属性 `computed`（lazy & cacheable）。

###### 需求分析

1. 当前 `effect` 函数会立即执行传递给它的副作用函数，有些场景下不希望它立即执行，例如计算属性。
2. 在需求 1 的基础上实现。

###### 解决方案

1. 通过给 `effect` 函数传递的 `options` 中添加 `lazy` 属性。[修改记录](https://gitee.com/jokerwon/my-vue/commit/5de5cb47f03018ca9e50b3a2fc568f01d7b40029)

   ~~~js
   function effect(fn, options = {}) {
       // 只有非 lazy 时才执行
       if (!options.lazy) {
           // 执行副作用函数
           effectFn();
       }
       // 将副作用函数作为返回值返回，在懒执行时可以交由用户决定执行时机
       return effectFn;
   }
   
   // 使用
   const effectFn = effect(
       () => {
           console.log(obj.foo);
       },
       {
           lazy: true,
       }
   );
   effectFn();
   ~~~

2. 把一个对象某个属性的 `getter` （`computed` 的功能也只需要 `getter` ）传递给 `effect` ，在触发 `getter` 的时候执行副作用函数。[修改记录](https://gitee.com/jokerwon/my-vue/commit/d66688898095ef6a5643425fda4ab6ed6ed8b3e2)

   ~~~js
   // v0.1
   function computed(getter) {
       // 把 getter 作为副作用函数，创建一个 lazy 的 effect
       // 此时 effectFn 的返回值就是 getter 的返回值
       const effectFn = effect(getter, { lazy: true });
   		// 创建一个临时对象，用来当作载体
       const obj = {
           get value() {
               return effectFn();
           },
       };
       return obj;
   }
   // 使用
   const data = { foo: 1, bar: 2 }
   const obj = new Proxy(data, {/* ... */})
   
   const sumRes = computed(() => obj.bar + obj.foo);
   console.log(sumRes.value); // 3
   obj.bar++;
   obj.foo++;
   console.log(sumRes.value); // 5
   console.log(sumRes.value); // 5，又计算了一次
   ~~~

   但是 v0.1 版本不支持缓存。创建一个变量缓存计算值，另创建一个标识 `dirty` 判断是否需要更新缓存，并在依赖修改的时候将 `dirty` 设置为 `true` 。[修改记录](https://gitee.com/jokerwon/my-vue/commit/21ed09c6145e4c9cda47f2ff4f053238cd1fbb93)

   ~~~js
   // v0.2
   function computed(getter) {
       const effectFn = effect(getter, {
           lazy: true,
           scheduler() {
               // 当调度器被执行时，说明依赖项被更新了，即 computed 的缓存失效了，需要重新计算
               dirty = true;
           },
       });
     	// 用于缓存上一次计算的值
       let value;
       // 用来标识是否需要重新计算值，true 表示需要重新计算
       let dirty = true;
       const obj = {
           get value() {
               if (dirty) {
                   // 重新计算，并更新缓存
                   value = effectFn();
                   // 将 dirty 设置为 false，在 dirty 被重新设置为 true 之前，缓存值 value 一直有效
                   dirty = false;
               }
               return value;
           },
       };
       return obj;
   }
   // 使用
   const data = { foo: 1, bar: 2 }
   const obj = new Proxy(data, {/* ... */})
   const sumRes = computed(() => obj.bar + obj.foo);
   console.log(sumRes.value); // 3
   obj.bar++;
   obj.foo++;
   console.log(sumRes.value); // 5
   console.log(sumRes.value); // 5，不会重新计算
   ~~~

   然而，v0.2 版本还存在问题，在另外的 `effect` 中访问计算属性的值时，当值修改了，该 `effect` 不会自动执行。

   ~~~js
   const sumRes = computed(() => obj.bar + obj.foo)
   effect(() => {
     // 在该副作用函数中读取 sumRes.value
     console.log(sumRes.value)
   })
   // 修改 obj.foo 导致 sumRes.value 修改，但是上面的 effect 没有再次执行
   obj.foo++
   ~~~

   究其原因，本质上是发生了 `effect` 嵌套，响应式数据没有将外层的  `effect` 收集到依赖中。解决办法是当读取计算属性时，手动调用 `track` 函数进行追踪，当计算属性依赖的响应式数据变化时，手动调用 `trigger` 触发响应。[修改记录](https://gitee.com/jokerwon/my-vue/commit/bf9bfda1f5e7ce51d0ed96f41e9ba6439d57c9d3)

   ~~~js
   // v1.0
   function computed(getter) {
       const effectFn = effect(getter, {
           lazy: true,
           scheduler() {
               if (!dirty) {
                   // 当调度器被执行时，说明依赖项被更新了，即 computed 的缓存失效了，需要重新计算
                   dirty = true;
                   // 当计算属性依赖的响应式数据更新时手动调用 trigger 触发响应
                   trigger(obj, "value");
               }
           },
       });
       let value;
       let dirty = true;
       const obj = {
           get value() {
               if (dirty) {
                   value = effectFn();
                   dirty = false;
               }
               // 当读取时手动调用 track 追踪变化
               track(obj, "value");
               return value;
           },
       };
       return obj;
   }
   // 使用
   const data = { foo: 1, bar: 2 }
   const obj = new Proxy(data, {/* ... */})
   const sumRes = computed(() => obj.bar + obj.foo)
   effect(effectFn() {
     console.log(sumRes.value)
   })
   obj.foo++
   
   // 它们建立了这样的联系
   bucket
   	|--> data
   				|--> () => obj.bar + obj.foo
     |--> computed(() => obj.bar + obj.foo)
   			  |--> effectFn
   ~~~



##### 4.9 watch 的实现原理

###### 新需求

支持 `watch`

###### 解决方案

利用 `effect` 和 `options.scheduler` 。[修改记录](https://gitee.com/jokerwon/my-vue/commit/dae666b5f4db0ad813173c4467802753b7eba26a)

~~~js
function watch(source, cb) {
    // getter 用来读取原对象，以收集依赖
    let getter;
    // 支持传入一个 getter 进来
    if (typeof source === "function") {
        getter = source;
    } else {
        getter = () => traverse(source);
    }

    let oldVal, newVal;
    // FIXME: 当监听整个响应式对象时，oldVal 和 newVal 总是一样的
    const effectFn = effect(() => getter(), {
        lazy: true,
        scheduler() {
            // 在调度器中重新执行副作用函数，得到最新值
            newVal = effectFn();
            cb(newVal, oldVal);
            // 此次的新值就是下次更新的旧值，用此次的新值替换旧值
            oldVal = newVal;
        },
    });
    // 先手动调用副作用函数，作为第一个旧值
    oldVal = effectFn();
}

function traverse(value, seen = new Set()) {
    // 如果 value 是原始值或者已经被读取过，则什么都不做
    if (typeof value !== "object" || value === null || seen.has(value))
        return value;

    // 将 value 放入 seen 集合中，表示已经遍历过，避免循环引用造成死循环
    seen.add(value);

    // 循环递归读取 value 的属性
    for (const key in value) {
        traverse(value[key]);
    }

    return value;
}
~~~



##### 4.10 立即执行的 watch 与回调执行时机

###### 新需求

支持立即执行和控制回调执行的时机

###### 解决方案

增加 `options` 入参，通过 `options.immediate` 来控制是否立即执行，通过 `options.flush` 控制执行时机。[修改记录](https://gitee.com/jokerwon/my-vue/commit/d1fd6d99023bebe4a2b9cf93a1277d6c89c80bb9)

~~~js
function watch(source, cb, options = { immediate: false, flush: "sync" }) {
    let getter;
    if (typeof source === "function") {
        getter = source;
    } else {
        getter = () => traverse(source);
    }
    let oldVal, newVal;
  	// >>>> 修改
    const job = () => {
        // 在调度器中重新执行副作用函数，得到最新值
        newVal = effectFn();
        cb(newVal, oldVal);
        // 此次的新值就是下次更新的旧值，用此次的新值替换旧值
        oldVal = newVal;
    };
    const effectFn = effect(() => getter(), {
        lazy: true,
        scheduler: () => {
            if (options.flush === "post") {
                Promise.resolve().then(job);
            } else {
                job();
            }
        },
    });
    if (options.immediate) {
        // 当 options.immediate 为 true 时，立即执行调度器
        job();
    } else {
        oldVal = effectFn();
    }
  	// >>>>
}
~~~



##### 4.11 过期的副作用

###### 新需求

解决竞态问题。

> 竞态问题：多个同样的请求发送后，过期的请求（较早发送的请求）依然修改了数据，导致数据时效性问题。

###### 解决方案

在 `watch` 的第二个参数，即回调函数中，多传递一个用于通知本次副作用过期的函数。[修改记录](https://gitee.com/jokerwon/my-vue/commit/e0d709217199c828bd892fe2218126e462fa7f46)

~~~js
function watch(source, cb, options = { immediate: false, flush: "sync" }) {
    let getter;
    if (typeof source === "function") {
        getter = source;
    } else {
        getter = () => traverse(source);
    }
    let oldVal, newVal;
  	// >>>> 修改
    // 用于保存用户注册的过期回调
    let cleanup;
    // 定义 onInvalidate 函数
    function onInvalidate(fn) {
        // 将过期回调保存到 cleanup 中
        cleanup = fn;
    }
    const job = () => {
        newVal = effectFn();
        // 每次调度器执行，说明之前的任务已经过期了
        // 在回调执行前，先调用过期回调
        if (typeof cleanup === "function") {
            cleanup();
        }
        // 将 onInvalidate 作为第三个参数，以便用户使用
        cb(newVal, oldVal, onInvalidate);
        oldVal = newVal;
    };
  	// >>>>
    const effectFn = effect(() => getter(), {
        lazy: true,
        scheduler: () => {
            if (options.flush === "post") {
                Promise.resolve().then(job);
            } else {
                job();
            }
        },
    });
    if (options.immediate) {
        job();
    } else {
        oldVal = effectFn();
    }
}
~~~



##### 4.12 总结

###### 1. 为什么使用 WeakMap 存储副作用？

WeakMap 对 key 是弱引用，不影响 GC，一旦 key 被 GC 回收，那么对应的键和值就访问不到了。因此 WeakMap 常用来存储那些只有当 key 被引用的对象存在时（没被回收）才有价值的信息。如果 target 对象没有任何引用了，说明用户侧不再需要它了，这时 GC 就会完成回收任务。



###### 2. 为什么 trigger 中依赖集合要复制后再进行遍历？

~~~js
function trigger(target, key) {
    const depsMap = bucket.get(target);
    if (!depsMap) return;
    const effects = depsMap.get(key);

	  // >>>> 复制依赖集合
    const effectsToRun = new Set(effects);
    effectsToRun.forEach(effect => effect());
}
~~~

因为依赖集合 `effects` 在遍历中会执行 `effect` 副作用函数，而 `effect` 每次调用前，会清除依赖集合 `effects` 对自身的关联，也就是**从 `effects` 中将自身删除**，但是副作用函数调用时，会触发 `track` 函数，导致自身**又被重新加入到 `effects` 中**，而此时 `effects` 的遍历仍在进行，根据 ECMA 的规范，在调用 `forEach` 遍历 `Set` 集合时，如果一个值已经被访问过，但该值被删除并重新添加到集合，如果此时 `forEach` 仍未结束，那么该值会被重新访问。因此，如果不重新复制的话，会**导致遍历进入无限循环**。



### 第五章 非原始值的响应式方案



##### 5.1 理解 Proxy 和 Reflect

任何在 `Proxy` 的拦截器中能够找到的方法，都能够在 `Reflect` 中找到同名函数。

###### 存在的问题

考虑之前的响应式实现

~~~js
const obj = {
  foo: 1,
  get bar() {
    // 这里的 this 指向谁?
    return this.foo;
  }
};
const p = new Proxy(obj, {
  get(target, key) {
    track(target, key);
    // 注意，这里没有用 Reflect.get 完成读取
    return target[key];
  }
});

effect(() => {
  p.bar;
});
p.foo++;
~~~

###### 分析问题

实际上，在 `obj.bar` 的 `getter` 中， `this` 指向的是原始对象 `obj` ，这样就绕过了代理对象，就无法建立响应联系。

###### 解决方案

通过 `Reflect.get` 去修正 `getter` 中的 `this` 指向。[修改记录](https://gitee.com/jokerwon/my-vue/commit/9a74d4050b1ba351952c7df37864cfd8a04b54f4)

~~~js
const p = new Proxy(obj, {
  get(target, key, receiver) {
    track(target, key);
    // 使用 Reflect.get 完成读取
    return Reflect.get(target, key, receiver);
  }
});
~~~

代理对象的 `get` 拦截函数接收的第三个参数 `receiver` ，代表谁在读取属性。例如：

~~~js
p.bar // 代理对象 p 在读取 bar 属性
~~~

此时，`receiver` 就是 `p` 。

使用 `Reflect.get(target, key, receiver)` 去访问 `bar` 属性时，它的 `getter` 函数中的 `this` 就会指向代理对象 `p` 了。



##### 5.2 JavaScript 对象及 Proxy 的工作原理

###### 常规对象(ordinary object)

- 对于所必须部署的 11 个必要的内部方法（`[[Get]]` 、`[[Set]]` 等），必须使用 ECMA 规范 10.1.x 节给出的定义实现。
- 对于内部方法 `[[Call]]` ，必须使用 ECMA 规范 10.2.1 节给出的定义实现。
- 对于内部方法 `[[Constructor]]` ，必须使用 ECMA 规范 10.2.2 节给出的定义实现。

###### 异质对象(exotic object)

任何不属于常规对象的对象都是异质对象。

###### 内部方法(internal method)

当我们对一个对象进行操作时**在引擎内部调用的方法**。内部方法对于 JavaScript 使用者是不可见的。

例如我们访问对象属性时，引擎内部会调用 `[[Get]]` 这个内部方法来读取属性值。

> 在 ECMAScript 规范中使用 [[xxx]] 来代表内部方法或内部槽。

###### 如何区分一个对象是普通对象还是函数？

通过内部方法和内部槽来区分对象，例如函数对象会部署内部方法 `[[Call]]` ，而普通对象则不会。



##### 5.3 如何代理 Object

***从本节开始实现响应式数据。***

本节[修改记录](https://gitee.com/jokerwon/my-vue/commit/c133792c9b0ac35a9a9db7527a0382a1583ef046) 。

响应系统应该拦截一切读取操作，以便当数据变化时能够正确地触发响应。读取操作和对应的 trap 方案如下：

- 访问属性： `obj.foo`

  使用 `get` 拦截。

  ~~~js
  get(target, key, receiver) {
  	track(target, key)
    return Reflect.get(target, key, receiver)
  }
  ~~~

- 判断对象或原型上是否存在给定的 key： `key in obj` 

  使用 `has` 拦截。

  ~~~js
  has(target, key) {
  	track(target, key)
    return Reflect.get(target, key)
  }
  ~~~

- 使用 `for...in` 循环遍历对象： `for (const key in obj)` 

  使用 `ownKeys` 拦截，并用唯一的 `ITERATE_KEY` 作为追踪的 `key` 。

  ~~~js
  const ITERATE_KEY = Symbol() // 创建一个唯一的 key 来关联遍历相关的副作用
  ownKeys(target) {
  	track(target, ITERATE_KEY)
    return Reflect.ownKeys(target)
  }
  ~~~

  为对象**添加新的属性**时，也应该触发与 `ITERATE_KEY` 相关联的副作用。所以需要优化 `trigger` 函数 。

  ~~~js
  function trigger(target, key, type) {
    const depsMap = bucket.get(target);
    if (!depsMap) return;
    const effects = depsMap.get(key);
    // >>>> 新增
    // 获取与 ITERATE_KEY 相关联的副作用函数
    const iterateEffects = depsMap.get(ITERATE_KEY);
    // ...
    // 将与 ITERATE_KEY 相关联的副作用函数也添加到 effectsToRun
    iterateEffects &&
        iterateEffects.forEach(effect => {
            if (effect !== activeEffect) {
                effectsToRun.add(effect);
            }
        });
    // >>>>
    effectsToRun.forEach(effect => {
        if (effect.options && effect.options.scheduler) {
            effect.options.scheduler(effect);
        } else {
            effect();
        }
    });
  }
  ~~~

  但是此时修改已存在属性的值时也会触发 `ITERATE_KEY` 关联的副作用函数，这并不是期望的，所以 `trigger` 需要知道此次操作是新增还是修改。

  在 `set` 拦截函数中区分属性的操作行为，并传递给 `effect` 。

  ~~~js
  // 定义触发类型
  const TriggerType = {
      SET: 'SET',
      ADD: 'ADD',
      DELETE: 'DELETE',
  };
  new Proxy(obj, {
    set(target, key, newVal) {
      // 如果属性不存在，说明是添加新属性，否则是设置已有属性
      const type = Object.prototype.hasOwnProperty.call(target, key) ? TriggerType.SET : TriggerType.ADD;
      // 设置属性值
      const res = Reflect.set(target, key, newVal, receiver);
      // 执行依赖的副作用函数，并将 type 作为第三个参数传递
      trigger(target, key, type);
      return res;
    },
  })
  ~~~

  同样地，**删除属性**也应该触发 `ITERATE_KEY` 关联的副作用函数。

  创建 `deleteProperty` 拦截函数。

  ~~~js
  new Proxy(obj, {
    // 拦截 delete 操作
    deleteProperty(target, key) {
      // 检查被操作的属性是否是对象自己的属性
      const hadKey = Object.prototype.hasOwnProperty.call(target, key);
      // 进行属性删除
      const res = Reflect.deleteProperty(target, key);
      if (res && hadKey) {
          // 只有当被删除属性是对象自己的属性且删除成功删除时，才触发更新
          trigger(target, key, TriggerType.DELETE);
      }
      return res;
    },
  });
  ~~~

  使 `effect` 响应属性新增和删除行为。

  ~~~js
  function trigger(target, key, type) {
    // ...
    // >>>> 修改
    // 只有当操作类型 type 为 ADD 或 DELETE 时，才触发
    if (type === TriggerType.ADD || type === TriggerType.DELETE) {
        // 将与 ITERATE_KEY 相关联的副作用函数也添加到 effectsToRun
        iterateEffects &&
          iterateEffects.forEach(effect => {
            // 如果 trigger 触发执行的副作用函数与当前正在执行的副作用函数相同，则不触发执行。
            if (effect !== activeEffect) {
                effectsToRun.add(effect);
            }
          });
    }
    // >>>>
    // ...
  }
  ~~~

  

##### 5.4 合理地触发响应

###### 存在的问题

1. 当值没有变化时，不应该触发响应。

2. 考虑如下使用场景，副作用会执行两次。

   ~~~js
   const obj = {};
   const proto = { bar: 1 };
   
   const child = reactive(obj);
   const parent = reactive(proto);
   Object.setPrototypeOf(child, parent);
   
   effect(() => {
       console.log(child.bar);
   });
   
   child.bar = 2;
   ~~~

###### 分析问题

2. 由于 `bar` 属性是存在 `child` 对象的原型 `parent` 对象中，所以 `child.bar` 会同时触发 `child` 和 `parent` 的 `get` 拦截函数，所以该副作用函数会同时被 `child.bar` 和 `parent.bar` 收集。当使用 `child.bar` 赋值的时候，

###### 解决方案

1. 在触发响应之前判断新值和旧值是否相等。[修改记录](https://gitee.com/jokerwon/my-vue/commit/89d9287d2b410b3d32604dd0d20e69797e64ea6c) 

   ~~~js
   set(target, key, newVal, receiver) {
     const oldVal = target[key];
     const type = Object.prototype.hasOwnProperty.call(target, key) ? TriggerType.SET : TriggerType.ADD;
     const res = Reflect.set(target, key, newVal, receiver);
     // 只有当新值和旧值不全等且都不是 NaN 时才触发响应
     if (oldVal !== newVal && (oldVal === oldVal || newVal === newVal)) {
         // 执行依赖的副作用函数，并将 type 作为第三个参数传递
         trigger(target, key, type);
     }
     return res;
   }
   ~~~

   


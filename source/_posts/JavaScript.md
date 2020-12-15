---
title: JavaScript
tag: [Frontend, JavaScript]
---


## 一、对象

### 1. 属性的简洁表示法
<!-- more -->

~~~javascript
// 属性简写
const foo = 'bar';
const baz = { foo };
// 等同于
const baz = { foo: foo }

// 方法简写
const o = {
  method() {
    //...
  }
}
// 等同于
const o = {
  method: function() {
    //...
  }
}
~~~

### 2. 属性名表达式

~~~javascript
let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123,
  ['h'+'ello']() {}
};
~~~

### 3. 属性的可枚举性和遍历

1) 可枚举性
对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。
`Object.getOwnPropertyDescriptor`方法可以获取该属性的描述对象。

~~~javascript
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,  //可枚举性
//    configurable: true
//  }
~~~

目前，有四个操作会忽略enumerable为false的属性。

- ``for...in``循环：只遍历对象自身的和继承的可枚举的属性。
- ``Object.keys()``：返回对象自身的所有可枚举的属性的键名。
- ``JSON.stringify()``：只串行化对象自身的可枚举的属性。
- ``Object.assign()``： 忽略``enumerable``为``false``的属性，只拷贝对象自身的可枚举的属性。

2) 属性的遍历

**（1）for...in**

`for...in`循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

**（2）Object.keys(obj)**

`Object.keys`返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

**（3）Object.getOwnPropertyNames(obj)**

`Object.getOwnPropertyNames`返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。

**（4）Object.getOwnPropertySymbols(obj)**

`Object.getOwnPropertySymbols`返回一个数组，包含对象自身的所有 Symbol 属性的键名。

**（5）Reflect.ownKeys(obj)**

`Reflect.ownKeys`返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

## 二、Symbol

## 三、Set和Map

## 四、Proxy

## 五、Reflect

## 六、Promise

## 七、Generator

## 八、class 关键字

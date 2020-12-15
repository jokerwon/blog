---
title: Interview
---

## 前言

收集前端方面高频面试题。

<!-- more -->

## HTML 和 CSS

### 1. HTML

#### 语义化

#### 多媒体

#### SEO

### 2. CSS

#### 布局

##### 水平垂直居中

1. ##### 定位

   ~~~css
   .father {
     position: relative;
   }
   .son {
     position: absolute;
     top: 50%;
     left: 50%;
     width: 200px;
     height: 100px;
     background-color: #2797ff;
     /* 已知宽高 */
     margin-top: -50px;
     margin-left: -100px;
     /* 未知宽高 */
     transform: translate(-50%, -50%);
   }
   ~~~

2. ##### flex

   ~~~css
   .father {
     display: flex;
     justify-content: center;
     aligh-items: center
   }
   ~~~

##### 双飞翼布局

~~~html
<body class="clearfix">
  <div class="container">
    <div class="center"></div>    
  </div>
  <div class="left"></div>
  <div class="right"></div>
</body>
~~~

~~~css
.container {
  float: left;
  width: 100%;
}

.center {
  height: 200px;
  margin-left: 110px;
  margin-right: 220px;
  background: green;
}

.center::after {
  content: '';
  display: block;
  font-size: 0;
  height: 0;
  zoom: 1;
  clear: both;
}

.left {
  float: left;
  height: 200px;
  width: 100px;
  margin-left: -100%;
  background: red;
}

.right {
  float: right;
  height: 200px;
  width: 200px;
  margin-left: -200px;
  background: blue;
}
~~~

##### 圣杯布局

~~~html
<div class="container clearfix">
  <div class="center"></div>
  <div class="left"></div>
  <div class="right"></div>
</div>
~~~

~~~css
.container {
  margin-left: 120px;
  margin-right: 220px;
}

.main {
  float: left;
  width: 100%;
  height: 300px;
  background: green;
}

.left {
  position: relative;
  left: -120px;
  float: left;
  height: 300px;
  width: 100px;
  margin-left: -100%;
  background: red;
}

.right {
  position: relative;
  right: -220px;
  float: right;
  height: 300px;
  width: 200px;
  margin-left: -200px;
  background: blue;
}
~~~

#### 盒子模型

##### 标准盒模型（content-box）

盒子大小 = content(width) + padding + border + margin

##### 怪异盒模型（旧版本IE）(border-box)

盒子大小 = (content + padding + border)(width) + margin

##### Flex 盒模型

#### BFC

- 触发条件
  - body 根元素
  - 浮动元素：float 除 none 以外的值
  - 绝对定位元素：position (absolute、fixed)
  - display 为 inline-block、table-cells、flex
  - overflow 除了 visible 以外的值 (hidden、auto、scroll)

- 特性及应用
  1. 同一个 BFC 下外边距会发生折叠。
  2. BFC 可以包含浮动元素。（应用场景：清除浮动）
  3. BFC 可以阻止元素被浮动元素覆盖。

#### Flex

##### 容器属性

- flex-direction

  主轴方向，row ｜ column ｜ row-reverse ｜ column-reverse

- flex-wrap

  换行方式，nowrap ｜ wrap ｜ wrap-reverse

- flex-flow

  <flex-direction> || <flex-wrap>，默认 row  nowrap。

- justify-content

  定义主轴的对齐方式。

  - `flex-start`（默认值）：左对齐
  - `flex-end`：右对齐
  - `center`： 居中
  - `space-between`：两端对齐，项目之间的间隔都相等。
  - `space-around`：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

- align-items

  定义交叉轴的对齐方式。

  - `flex-start`：交叉轴的起点对齐。
  - `flex-end`：交叉轴的终点对齐。
  - `center`：交叉轴的中点对齐。
  - `baseline`: 项目的第一行文字的基线对齐。
  - `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

- align-content

  定义多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

  - `stretch`（默认值）：轴线占满整个交叉轴。

  - `flex-start`：与交叉轴的起点对齐。
  - `flex-end`：与交叉轴的终点对齐。
  - `center`：与交叉轴的中点对齐。
  - `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
  - `space-around`：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。

##### 项目属性

- order

  定义项目的排列顺序。数值越小，排列越靠前，默认为0。

- flex-grow

  定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

- flex-shrink

  定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

- flex-basis

  定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

- flex

  `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

  该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

- align-self

  `align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

#### Grid

##### 容器属性

- grid-template-columns & grid-template-rows

  `grid-template-columns`属性定义每一列的列宽，`grid-template-rows`属性定义每一行的行高。

  ~~~css
  .container {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 33.33% 33.33% 33.33%;
  }
  ~~~

  - repeat()

    ~~~css
    .container {
      display: grid;
      grid-template-columns: repeat(3, 100px);
      grid-template-rows: repeat(3, 33.33%);
      /* 重复某种模式 */
      grid-template-columns: repeat(2, 100px 20px 80px);
      /* 相当于 */
      grid-template-columns: 100px 20px 80px 100px 20px 80px;
    }
    ~~~

  - auto-fill 关键字

    有时，单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用`auto-fill`关键字表示自动填充。

    ~~~css
    /* 表示每列宽度100px，然后自动填充，直到容器不能放置更多的列 */
    .container {
      display: grid;
      grid-template-columns: repeat(auto-fill, 100px);
    }
    ~~~

  - fr 关键字

    为了方便表示比例关系，网格布局提供了`fr`关键字（fraction 的缩写，意为"片段"）, 表示剩余空间。如果两列的宽度分别为`1fr`和`2fr`，就表示后者是前者的两倍。

    ~~~css
    .container {
      display: grid;
      grid-template-columns: 150px 1fr 2fr;
    }
    ~~~

  - minmax()

    `minmax()`函数产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。

    ~~~css
    /* 列宽不小于100px，不大于1fr */
    grid-template-columns: 1fr 1fr minmax(100px, 1fr);
    ~~~

  - auto 关键字

    表示由浏览器自己决定长度。

    ~~~css
    /* 第二列的宽度，基本上等于该列单元格的最大宽度，除非单元格内容设置了min-width，且这个值大于最大宽度 */
    grid-template-columns: 100px auto 100px;
    ~~~

- grid-row-gap & grid-column-gap & grid-gap

  `grid-row-gap`属性设置行与行的间隔（行间距），`grid-column-gap`属性设置列与列的间隔（列间距）。

  ~~~css
  .container {
    grid-row-gap: 20px;
    grid-column-gap: 20px;
  }
  ~~~

  `grid-gap`属性是`grid-column-gap`和`grid-row-gap`的合并简写形式，语法如下。

  ~~~css
  grid-gap: <grid-row-gap> <grid-column-gap>;
  ~~~

- grid-template-areas

  网格布局允许指定"区域"（area），一个区域由单个或多个单元格组成。`grid-template-areas`属性用于定义区域。

  ~~~css
  /* 先划分出9个单元格，然后将其定名为a到i的九个区域，分别对应这九个单元格 */
  .container {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 100px 100px 100px;
    grid-template-areas: 'a b c'
                         'd e f'
                         'g h i';
  }
  ~~~

  **重名的单元格会合并**：

  ~~~css
  /* 顶部是页眉区域header，底部是页脚区域footer，中间部分则为main和sidebar */
  grid-template-areas: "header header header"
                       "main main sidebar"
                       "footer footer footer";
  ~~~

  不需要利用的区域可以用 `.` 来表示：

  ~~~css
  /* 中间一列为点，表示没有用到该单元格，或者该单元格不属于任何区域 */
  grid-template-areas: 'a . c'
                       'd . f'
                       'g . i';
  ~~~

  ⚠️ 注意

  区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为`区域名-start`，终止网格线自动命名为`区域名-end`。

  比如，区域名为`header`，则起始位置的水平网格线和垂直网格线叫做`header-start`，终止位置的水平网格线和垂直网格线叫做`header-end`。

- justify-items & align-items & place-items （**设置单元格对齐方式**）

  `justify-items` 属性设置单元格内容的水平位置（左中右），`align-items` 属性设置单元格内容的垂直位置（上中下）。

  - `start`：对齐单元格的起始边缘。
  - `end`：对齐单元格的结束边缘。
  - `center`：单元格内部居中。
  - `stretch`：拉伸，占满单元格的整个宽度（默认值）。

  `place-items` 属性是 `align-items` 属性和 `justify-items` 属性的合并简写形式。

  ```css
  place-items: <align-items> <justify-items>;
  ```

- justify-content & align-content & place-content

  `justify-content` 属性是整个内容区域在容器里面的水平位置（左中右），`align-content` 属性是整个内容区域的垂直位置（上中下）。

  - `start` : 对齐容器的起始边框。
  - `end` : 对齐容器的结束边框。
  - `center` : 容器内部居中。
  - `stretch` : 项目大小没有指定时，拉伸占据整个网格容器。
  - `space-around` : 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与容器边框的间隔大一倍。
  - `space-between` : 项目与项目的间隔相等，项目与容器边框之间没有间隔。
  - `space-evenly` : 项目与项目的间隔相等，项目与容器边框之间也是同样长度的间隔。

  `place-content` 属性是 `align-content` 属性和 `justify-content` 属性的合并简写形式。

  ```css
  place-content: <align-content> <justify-content>
  ```

##### 项目属性

- grid-column-start：左边框所在的垂直网格线
  grid-column-end：右边框所在的垂直网格线
  grid-row-start：上边框所在的水平网格线
  grid-row-end：下边框所在的水平网格线

  ```css
  /* 左边框是第二根垂直网格线，右边框是第四根垂直网格线，上下边框使用默认配置 */
  .item-1 {
    grid-column-start: 2;
    grid-column-end: 4;
  }
  ```

  `grid-column`属性是`grid-column-start`和`grid-column-end`的合并简写形式，`grid-row`属性是`grid-row-start`属性和`grid-row-end`的合并简写形式。

  ```css
  .item {
    grid-column: <start-line> / <end-line>;
    grid-row: <start-line> / <end-line>;
  }
  
  .item-1 {
    grid-column: 1 / 3;
    grid-row: 1 / 2;
  }
  /* 等同于 */
  .item-1 {
    grid-column-start: 1;
    grid-column-end: 3;
    grid-row-start: 1;
    grid-row-end: 2;
  }
  ```

  斜杠以及后面的部分可以省略，默认跨越一个网格。

- grid-area

  指定项目放在哪一个区域。

  ```css
  .item-1 {
    grid-area: e;
  }
  ```

  `grid-area`属性还可用作`grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`的合并简写形式，直接指定项目的位置。

  ```css
  .item {
    grid-area: <row-start> / <column-start> / <row-end> / <column-end>;
  }
  
  .item-1 {
    grid-area: 1 / 1 / 3 / 3;
  }
  ```

- justify-self & align-self & place-self

  `justify-self`属性设置单元格内容的水平位置（左中右），跟`justify-items`属性的用法完全一致，但只作用于单个项目。

  `align-self`属性设置单元格内容的垂直位置（上中下），跟`align-items`属性的用法完全一致，也是只作用于单个项目。

  ```css
  .item {
    justify-self: start | end | center | stretch;
    align-self: start | end | center | stretch;
  }
  ```

  `place-self`属性是`align-self`属性和`justify-self`属性的合并简写形式。

  ```css
  place-self: <align-self> <justify-self>;
  ```

#### 响应式布局

##### 1. @media

##### 2. rem

##### 3. flex

##### 4. vh/vw

## JavaScript

### 1. 闭包

闭包是指有权访问另一个函数作用域中的变量的函数。当产生闭包时，闭包中的变量会常驻内存，所以使用时要注意内存泄漏问题。

在定时器、事件监听、ajax 请求等任务中，只要使用了回调函数，实际上就是在使用闭包。

闭包一般被用来封装私有变量，例如 IIFE 中就利用了闭包来进行封装。

### 2. 作用域

#### 词法作用域

词法作用域就是定义在词法阶段的作用域，简单来说可以指编写代码时所产生的作用域。与词法作用域对应的是动态作用域。

#### 函数作用域

函数作用域是指属于这个函数的全部变量可以在整个函数范围内（包括其内部嵌套的函数作用域）使用以及复用。在函数作用域外部无法直接访问内部的变量。

浏览器中存在一个全局作用域，指向 `window` 对象。Node 环境中的全局作用域指向的是 `global` 对象。

#### 作用域链

**由内层的函数作用域层层牵引到全局作用域的这个过程，产生了一条作用域链。**作用域链是为了保证对执行环境有权访问的所有变量和函数的有序访问。

#### 块级作用域

在 ES6 之前，大概只有 `with` 和`try...catch...` 的 `catch`分支中存在块级作用域。ES6 新增了 `let`  和 `const` 关键字，从而方便地实现了块级作用域。

在有 `let` 或 `const` 声明的块级作用域中，会产生一个暂时性死区，就是说由 `let` 声明的变量，在声明之前都是无法使用的，这意味着 `let` 和 `const` 不存在变量提升，由其声明的变量也无法重复声明。

### 3. 原型

#### 原型对象

无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个 `prototype` 属性，这个属性指向函数的原型对象。在默认情况（重写原型对象可能会造成 `constructor` 属性丢失），所有原型对象会自动获得一个 `constructor` 属性，指向 `prototype` 所在的函数。在检索某个对象的属性时，会先从对象本身开始搜索，如果搜索到则直接返回，否则会去对象的 `[[prototype]]` 所指向的原型对象中搜索。

#### 原型链

如果让原型对象指向另一个类型的实例，那么原型对象中将包含一个指向另一个原型的指针（`[[prototype]]`），加入另一个原型也是其他某一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了原型链。

### 4. 创建对象的方法

#### 1) 工厂模式

~~~js
function createPerson(name, age, job) {
  let o = new Object();
  o.name= name;
  o.age = age;
  o.job = job;
  return o;
}
let person1 = createPerson("Sam", 22, "FE");
let person2 = createPerson("Tam", 24, "BE");
~~~

**问题：**

1. 无法识别对象类型

#### 2) 构造函数

~~~js
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() {
    console.log(this.name);
  };
}
let person1 = new Person("Sam", 22, "FE");
let person2 = new Person("Tam", 24, "BE");
~~~

**问题：**

1. 无法共享方法

#### 3) 原型模式

~~~js
function Person() {}
Person.prototype.name = "Sam";
Person.prototype.age = 22;
Person.prototype.job = "FE";
Person.prototype.friends = ["Tim", "Cat"];
Person.prototype.sayName = function() {
  console.log(this.name);
};
let person1 = new Person();
let person2 = new Person();
~~~

**注意：**

1. 原型的动态性：由于在原型中查找值的过程是一次搜索，所以原型对象的任何修改都能立即在从实例上反映出来。

**问题：**

1. 未单独初始化属性，每个实例属性默认值都相同。
2. 复杂数据类型共享问题。

#### 4) 组合使用构造函数模式和原型模式

~~~js
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = ["Tim", "Cat"];
}
Person.prototype.sayName = function() {
  console.log(this.name);
};
let person1 = new Person("Sam", 22, "FE");
let person2 = new Person("Tam", 24, "BE");
~~~

**问题：**

1. 属性和方法分开定义。

#### 5) 动态原型模式

~~~js
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = ["Tim", "Cat"];
  if(typeof this.sayName != "function") {
    Person.prototype.sayName = function() {
    console.log(this.name);
  };
  }
}
let person1 = new Person("Sam", 22, "FE");
~~~

#### 6) 寄生构造函数模式

~~~js
function Person(name, age, job) {
  let o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function() {
    console.log(this.name);
  };
  return o;
}
let person1 = new Person("Sam", 22, "FE");
~~~

**问题：**

1. 无法用 `instanceof` 来确定对象类型。

#### 7) 稳妥构造函数模式

~~~js
function Person(name, age, job) {
  let o = new Object();
  //在此处定义私有成员和方法
  ...
  o.sayName = function() {
    console.log(name);  // 不使用 this
  };
  return o;
}
// 只有 sayName 可以访问成员
~~~

稳妥对象：指没有公共属性，其方法也不引用 `this` 的对象。

与寄生构造函数模式有两点不同：

1. 新创建对象的实例方法不引用 `this`。
2. 不适用 `new` 操作符调用构造函数。

### 5. 继承

#### 1) 原型链继承

~~~js
function Super() {}
function Sub() {}
Sub.prototype = new Super();
Sub.prototype.constructor = Sub;
~~~

**问题：**

1. 包含引用类型属性的原型存在共享问题。
2. 创建子类时无法向父类构造函数传参。

#### 2) 借用构造函数

~~~js
function Super() {}
function Sub() {
  Super.call(this, ...args);
}
~~~

**优势：**

1. 可以向父类构造函数传参。

**问题：**

1. 当父类方法在原型中定义时，无法进行函数复用，即**无法继承父类的原型中的属性**。

#### 3) 组合继承

~~~js
function Super() {}
function Sub() {
  Super.call(this, ...args);  // 第二次调用父类构造函数
}
Sub.prototype = new Super();  // 第一次调用父类构造函数
Sub.prototype.constructor = Sub;
~~~

**问题：**

1. 调用两次父类构造函数。

#### 4) 原型式继承

~~~js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
let person = {
  name: "John",
  friends: ["Tim", "Cat"]
}
let anotherPerson = object(person);  // 可以用 Object.create(person)
anotherPerson.name = "Tam";
anotherPerson.friends.push("Jerry");
person.friends  // ["Tim", "Cat", "Jerry"],修改了原型中的属性
~~~

**说明：**

1. `Object.create()` 只传一个参数时的行为与 `object()` 相同。
2. 其第二个参数与 `Object.defineProperties()` 的第二个参数相同，用于覆盖原型对象上的同名属性。

#### 5) 寄生式继承

~~~js
function createAnother(original) {
  let clone = object(original);  // 可以用 Object.create(person)
  clone.sayHi = function() {
    console.log("hi");
  }
}
~~~

#### 6) 寄生组合式继承

~~~js
function inheritPrototype(subType, superType) {
  let prototype = object(superType.prototype);  // 可以用 Object.create(person)
  prototype.constructor = subType;
  subType.prototype = prototype;
}

function Super() {}
function Sub(name) {
  Super.call(this, ...args);  // 第二次调用父类构造函数
  this.name = name
}
// Sub.prototype = new Super();  // 第一次调用父类构造函数
inheritPrototype(Sub, Super);
Sub.prototype.sayName = function() {
  console.log(this.name)
};
~~~

### 6. new

1. 创建一个空的简单JavaScript对象（即`{}`）；
2. 链接该对象（即设置该对象的构造函数）到另一个对象（构造器的原型对象） ；
3. 将步骤1新创建的对象作为`this`的上下文 ；
4. 如果该函数没有返回对象，则返回`this`。

### 7. this

#### 定义

`this` 是在运行时进行绑定的，而非编写时绑定。当一个函数被调用时，会创建一个活动记录（执行上下文）。这个记录会包含函数被调用的位置、函数的调用方式、传入的参数等信息。`this` 是这个记录的一个属性，会在函数执行的过程中用到。

#### 绑定规则

##### 1) 默认绑定

独立函数调用。如果使用严格模式（strict mode），则不能将全局对象用于默认绑定，因此this会绑定到 `undefined` 。

##### 2) 隐式绑定

调用位置有上下文对象，或者被某个对象拥有或者包含时，可能会产生隐式绑定。当函数引用有上下文对象时，隐式绑定规则会把函数调用中的this绑定到这个上下文对象。

~~~js
function foo() {
  console.log(this.a);
}
let obj2 = {
  a: 42,
  foo: foo
};
let obj1 = {
  a: 2,
  obj2: obj2
};
obj1.obj2.foo();  // 42
~~~

**隐式丢失**

一个最常见的this绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把 `this` 绑定到全局对象或者 `undefined` 上，取决于是否是严格模式。

~~~js
function foo() {
  console.log(this.a);
}

function doFoo(fn) {
  fn();
}

let obj = {
  a: 2,
  foo: foo
};
var a = "ops, global";
doFoo(obj.foo);  // ops, gloabal
~~~

##### 3) 显示绑定

直接指定this的绑定对象。

**硬绑定**

使用 `call`, `apply` , `bind` 进行 `this` 绑定。

**API 调用的上下文**

例如 `forEach` 的第二个参数。

##### 4) new 绑定

使用 `new` 来调用构造函数时会创建一个新对象，并把该对象绑定到构造函数调用中的 `this` 上。

##### 优先级

一般情况下，new绑定 > 显示绑定 > 隐式绑定 > 默认绑定。

如果将 this 利用`call`,  `apply` 或 `bind`显示绑定到 `null` 或者 `undefined` 上，这些值会被忽略，实际应用的是默认绑定。

间接引用时，调用函数会应用默认绑定。

### 8. 事件循环

### 9. 垃圾回收

- **标记清除**
- **引用计数**

### 10. DOM 和 BOM

### 11. 模块化

### 12. 跨域

#### 12.1 JSONP

#### 12.2 document.domain + iframe

#### 12.3 location.hash + iframe

#### 12.4 window.name + iframe

#### 12.5 postMessage

#### 12.6 CORS(跨域资源共享)

#### 12.7 nginx 代理

#### 12.8 nodejs 中间件代理

#### 12.9 websocket

### 13. 客户端存储

## HTTP
